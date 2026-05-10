---
title: "Skills in Claude: How a 30-Line Agent Beats a 3,000-Line One" 
date: 2026-05-10T08:06:33+11:00
draft: false
showToc: true
TocOpen: true
tags: ["claude", "agents", "skills", "ai", "node"]
categories: ["AI", "Claude","Agentic AI"]
description: "A beginner-friendly walkthrough of the 'skill' pattern in Claude agents - what it is, how it works, and a complete grading agent example from input CSV to final report."
author: "Prakash Bhandari"
toc: true
showReadingTime: true
cover:
  image: "images/posts/skills-in-the-claude-30-line-agent-vs-3000-line-agent/skills-in-the-claude-30-line-agent-vs-3000-line-agent.png" 
  alt: "Skills in Claude: How a 30-Line Agent Beats a 3,000-Line One"
  caption: "Skills in Claude: How a 30-Line Agent Beats a 3,000-Line One"
  relative: false
  author: "Prakash Bhandari"
---

## What We Are Building

By the end of this post you will have a working AI agent that:

1. Reads a CSV of student marks
2. Calculates a letter grade (A, B+, C−, etc.) and a GPA (4.0 scale) for each subject
3. Computes each student's overall grade
4. Writes a graded CSV and a formatted markdown report

The entire agent is **thirty lines of JavaScript**. The grading rules, the output format, and the report layout all live in plain markdown files - not in the code. That separation is what this post is about.


## Quick Concepts (Before We Dive In)

**What is an AI agent?**
An agent is a program that takes a plain-English instruction and figures out how to carry it out - reading files, writing output, running calculations. Unlike a chatbot that just responds to messages, an agent actually *does* things.

**What is the Claude Agent SDK?**
It is a JavaScript/TypeScript library from Anthropic that makes it easy to build agents powered by Claude. It handles sending your prompt to Claude, letting Claude call tools (like reading or writing files), and streaming the response back to you. Install it with:

```bash
npm install @anthropic-ai/claude-agent-sdk
```

**What is a skill?**
A skill is a folder you drop into `.claude/skills/`. Inside is a `SKILL.md` file that tells Claude what the skill does and how to do it - written in plain markdown, not code. The SDK finds skills automatically at startup. You will see exactly how in the example below.


## The Problem Skills Solve

Imagine writing a grading agent the naive way:

```js
const systemPrompt = `You are a grading assistant.
Grade rules: 93-100 = A (4.0), 90-92 = A- (3.7), 87-89 = B+ (3.3) ...
Output a CSV with columns: name, math_letter, math_gpa, overall_letter ...`;
```

This works on day one. On day two the teacher says: *"Can we move the A boundary to 95%?"* Now you - a developer - have to edit a string buried in a source file, redeploy the service, and hope nothing else broke.

Skills fix this by moving the grading rules out of the code and into a plain markdown file. The teacher can edit the rules themselves. The agent code never changes.


## Project Structure

Here is the full layout of `grade-agent`:

```
grade-agent/
├── src/
│   └── index.js                    ← the entire agent (30 lines)
├── data/
│   └── sample-marks.csv            ← input: raw student marks
└── .claude/
    └── skills/
        └── grade-csv/              ← one skill folder
            ├── SKILL.md            ← grading rules + step-by-step workflow
            └── templates/
                ├── graded.template.md   ← shape of the output CSV
                └── report.template.md   ← shape of the markdown report
```

That's it. Five meaningful files. Let's walk through each one.


## Step 1 - The Input (sample-marks.csv)

The agent reads a simple CSV. Each row is a student. Each column (after `name`) is a subject mark out of the configured maximum (default 100).

```csv
name,math,science,english
Alice,88,92,76
Bob,74,65,81
Carol,95,89,93
```

Nothing special here - this is a plain CSV a teacher could export from any spreadsheet.


## Step 2 - The Skill (SKILL.md)

This is where all the actual grading knowledge lives. The file has two parts.

### Part A: The Front Matter

The front matter sits at the very top between `---` lines. The SDK reads *only* this part during startup to know what the skill does:

```yaml
---
name: grade-csv
description: Calculate letter grades and 4.0-scale GPAs from a marks CSV.
  Use this skill whenever the user asks to grade marks, compute GPAs,
  build a report card, or calculate class averages - even if they don't
  say "GPA" explicitly.
---
```

The `description` is how the SDK matches your prompt to the right skill. When you ask Claude to "grade the marks", it reads this description and says: *"Yes, grade-csv matches."*

> **Beginner tip:** Write your description to cover the different ways a user might phrase the same request. The line *"even if they don't say 'GPA' explicitly"* is important - it catches natural language like "grade my class" that doesn't use technical terms.

### Part B: The Body

Below the front matter is the body - plain markdown. Claude reads this when the skill is active and follows it like a recipe:

```markdown
## Grading Rules

| Percentage | Letter | GPA  |
|------------|--------|------|
| 93–100     | A      | 4.0  |
| 90–92      | A-     | 3.7  |
| 87–89      | B+     | 3.3  |
| 83–86      | B      | 3.0  |
| 80–82      | B-     | 2.7  |
| 77–79      | C+     | 2.3  |
| 73–76      | C      | 2.0  |
| 70–72      | C-     | 1.7  |
| 67–69      | D+     | 1.3  |
| 63–66      | D      | 1.0  |
| 60–62      | D-     | 0.7  |
| < 60       | F      | 0.0  |

## Workflow

1. Confirm the input CSV path, output CSV path, report path, and per-subject maximum.
2. Read the input CSV. Each row is one student; columns after `name` are subjects.
3. For each student and each subject: percentage = mark ÷ maxMark × 100.
4. Look up the letter grade and GPA for each subject using the table above.
5. Compute the student's mean percentage across all subjects.
6. Look up the overall letter grade from the mean percentage.
   Do NOT average the per-subject GPA values - look up the grade from the mean
   percentage directly to avoid rounding errors.
7. Read templates/graded.template.md and write the output CSV to match its columns.
8. Read templates/report.template.md and write the report, filling every placeholder.
9. Print a one-paragraph class summary to stdout.
```

This is the entire grading policy. No Python, no JSON. A teacher can open this file in any text editor and understand it immediately. If the school changes its grading scale next year, someone edits one row in the table. The agent code stays the same.


## Step 3 - The Templates

Templates define the *shape* of the output - separate from the rules that compute it.

**`graded.template.md`** - describes the columns the output CSV must have:

```
name, {subject}_letter, {subject}_gpa, ..., average, overall_letter, overall_gpa
```

So for a CSV with `math`, `science`, and `english` subjects, the output gets columns:
`name`, `math_letter`, `math_gpa`, `science_letter`, `science_gpa`, `english_letter`, `english_gpa`, `average`, `overall_letter`, `overall_gpa`

**`report.template.md`** - the markdown report skeleton with placeholders:

```markdown
# Class Report

**Class average:** {{mean_average}}%  &nbsp; **Overall grade:** {{overall_letter}}

## Grade Distribution
{{distribution_table}}

## Student Results
{{students_table}}
```

Claude reads both templates before writing any output, then fills in every placeholder with the computed values.

> **Why separate templates?** Keeping layout in templates means you can change the report design without touching the grading rules - and vice versa. They are independent concerns in separate files.


## Step 4 - The Agent (index.js)

Here is the complete agent, with every line explained:

```js
import { query } from "@anthropic-ai/claude-agent-sdk"; // the SDK
import { resolve } from "path";                          // built-in Node path helper
import minimist from "minimist";                         // reads --flag=value from the command line

// Parse any flags the user passes, e.g. --input ./my-class.csv
const args = minimist(process.argv.slice(2));

// PROJECT_ROOT is the parent of the src/ folder
const PROJECT_ROOT = resolve(import.meta.dirname, "..");

// Use the passed flag, or fall back to a sensible default
const input   = args.input   ?? "data/sample-marks.csv";
const output  = args.output  ?? "data/output/graded.csv";
const report  = args.report  ?? "data/output/report.md";
const maxMark = args.maxMark ?? 100;

// Build a plain-English prompt - no grading rules live here
const prompt =
  `Grade the marks in "${input}". Use ${maxMark} as the per-subject ` +
  `maximum. Write the graded CSV to "${output}" and the report to "${report}".`;

// Send the prompt to Claude via the SDK.
// skills: "all" tells the SDK to discover and offer all skills under .claude/skills/
// allowedTools tells Claude which actions it is allowed to take
for await (const event of query({
  prompt,
  options: {
    cwd: PROJECT_ROOT,
    settingSources: ["project"],
    skills: "all",
    allowedTools: ["Bash", "Read", "Write"],
  },
})) {
  // Stream Claude's text responses to the terminal as they arrive
  if (event.type === "assistant" && event.message?.content) {
    for (const block of event.message.content) {
      if (block.type === "text") process.stdout.write(block.text);
    }
  }
}
```

**What is missing from this file?** Every single grading rule. The agent does not know what an A− is. It does not know that GPAs exist. It does not know what columns the output CSV should have. All of that lives in `SKILL.md` and the templates.

This is intentional. The agent is just plumbing. The skill is the product.


## Step 5 - How It All Fits Together

Here is exactly what happens from the moment you run `npm run grade`:

```
You type: npm run grade
          │
          ▼
index.js builds the prompt:
"Grade the marks in data/sample-marks.csv. Use 100 as the maximum..."
          │
          ▼
SDK scans .claude/skills/ and reads the front matter of every SKILL.md
(only the name + description - fast, no full bodies loaded yet)
          │
          ▼
SDK asks Claude: "Which skill matches this prompt?"
Claude reads the descriptions and answers: "grade-csv"
          │
          ▼
SDK loads the full body of grade-csv/SKILL.md into Claude's context
          │
          ▼
Claude follows the workflow step by step:
  → reads sample-marks.csv
  → computes percentages and looks up grades
  → reads graded.template.md, writes graded.csv
  → reads report.template.md, writes report.md
  → prints a summary paragraph to your terminal
          │
          ▼
Done. Two new files in data/output/
```

Nothing in this chain requires you to have written any grading logic in JavaScript.


## Step 6 - The Output

After running the agent against the sample CSV, you get:

**`data/output/graded.csv`:**

```csv
name,math_letter,math_gpa,science_letter,science_gpa,english_letter,english_gpa,average,overall_letter,overall_gpa
Alice,B+,3.3,A-,3.7,C+,2.3,85.3,B,3.0
Bob,C,2.0,D,1.0,B-,2.7,73.3,C,2.0
Carol,A,4.0,B+,3.3,A,4.0,92.3,A-,3.7
```

**`data/output/report.md`** (rendered):
```
> **Class average:** 83.6% &nbsp; **Overall grade:** B
>
> | Grade | Count |
> |-------|-------|
> | A-    | 1     |
> | B     | 1     |
> | C     | 1     |
>
> | Name  | Average | Overall |
> |-------|---------|---------|
> | Carol | 92.3%   | A-      |
> | Alice | 85.3%   | B       |
> | Bob   | 73.3%   | C       |
```
All of this was produced by thirty lines of JavaScript and a markdown file.


## Running It Yourself

You need Node 18+, Git, and an Anthropic API key.

```bash
# 1. Clone the repo
git clone https://github.com/dev-scripts/grade-agent.git
cd grade-agent

# 2. Install dependencies
npm install

# 3. Set your API key
cp .env.example .env
# Open .env and set: ANTHROPIC_API_KEY=sk-ant-...

# 4. Run it
npm run grade
```

You will see Claude's summary appear in the terminal, and find `graded.csv` and `report.md` in `data/output/`.

**Use your own CSV:**

```bash
node src/index.js \
  --input ./my-class.csv \
  --output ./my-class-graded.csv \
  --report ./my-class-report.md \
  --maxMark 50        # if marks are out of 50 instead of 100
```


## Try These Experiments

These hands-on experiments are the best way to feel how skills work.

**Experiment 1 - Change a grading rule without touching code:**

Open `.claude/skills/grade-csv/SKILL.md`. Find the row for `93–100 → A` and change it to `95–100 → A`. Add a row for `93–94 → A-`. Run the agent again. Carol's overall grade will change - same JavaScript, different behaviour, because only the skill changed.

**Experiment 2 - Add a column to the report without touching code:**

Open `templates/report.template.md` and add a `{{highest_score}}` placeholder. Then add a sentence to the workflow in `SKILL.md` saying "find the student with the highest average and insert their name at `{{highest_score}}`". Run the agent. The new column appears automatically.

**Experiment 3 - Add a brand-new skill:**

Create this file: `.claude/skills/summarize-feedback/SKILL.md`

```yaml
---
name: summarize-feedback
description: Summarise teacher feedback comments from a CSV. Use this skill
  when the user asks to summarise, review, or analyse teacher comments or feedback.
---

## Workflow
1. Read the input CSV. Each row has a student name and a teacher_comment column.
2. For each student, write a one-sentence summary of the comment.
3. Print the summaries as a markdown list.
```

Now run: `node src/index.js --input ./feedback.csv`

The agent routes to the new skill automatically. You did not change `index.js` at all.


## Conclusion

The skill pattern has one real caveat: write specific descriptions. The model's matching is only as good as the front matter prose - vague descriptions misfire, concrete trigger phrases don't.

Everything else is upside. Pull your embedded rules into a `SKILL.md`. Move your templates next to it. Replace the interpretation logic with `query({ skills: "all" })`. Keep going until your agent file is pure plumbing.

That's why a thirty-line agent outlives the three-thousand-line version every time.
