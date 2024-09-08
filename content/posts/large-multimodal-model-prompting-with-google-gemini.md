---
title: "Large Multimodal Model(LMM) Prompting With Google Gemini and Vertex AI"
date: 2024-09-07T17:26:54+10:00
showToc: true
TocOpen: true
tags : [
  "Large Multimodal Model",
  "LMM",
  "Artificial Intelligence",
  "AI",
]
categories : [
  "Artificial Intelligence"
]
keywords: [
  "Large Multimodal Model Prompting With Google Gemini",
  "Large Multimodal Model(LMM) Prompting With Google Gemini and Vertex AI",
  "Large Multimodal Model",
  "Artificial Intelligence",
  "LMM",
  "AI",
  "Artificial General Intelligence",
  "AGI",
  "Prompting",
  "Prompt Engineering",
  "Google's Gemini",
]
cover:
  image: "images/posts/large-multimodal-model-prompting-with-google-gemini-and-vertex-ai/large-multimodal-model.png" 
  alt: "Large Multimodal Model(LMM) Prompting With Google Gemini"
  relative: false
author: "Prakash Bhandari"
description: "A Large Multimodal Model (LMM) is an Artificial Intelligence (AI) model capable of processing and understanding multiple types of data modalities (such as text, audio, video, images, and potentially others) simultaneously, 
and providing outputs based on this information"
---
In this post, I will briefly define Large Multimodal Models (LMM), explain the data modalities of LMMs, 
and demonstrate Google Gemini's multimodal capabilities using text and images.
This is purely a no-code blog post.
I will demonstrate everything directly from the Google Cloud Console using
Vertex AI Studio and Vertex AI to showcase a simple example of using multimodal inputs (text file and image)

## Introduction
A Large Multimodal Model (LMM) is an Artificial Intelligence (AI) model capable of processing and understanding multiple types of input data modalities simultaneously and providing outputs based on this information. 
The input data can include a mix of audio, video, text, images, and even code.

Multimodal means one or more of the following:
1. Input and output are of different modalities (example: text-to-image, image-to-text, audio-to-text)
2. Inputs are multimodal (example: can process both text and images)
3. Outputs are multimodal (example: can generate both text and images)

An LMM is an advanced form of AI, which can be considered a step toward Artificial General Intelligence (AGI).

## Data modalities of LMM
### Audio
The model can understand sound, music, spoken language, and any other form of auditory inputs.
### Images
The model can understand objects, scenes, text, and even emotions from an image.
### Video
The model can understand visual and auditory elements form the video. It's a combination of audio and visual data
### Text
Text includes any form of content such as books, PDF files, text files, online news, articles, research papers, social media posts, and so on.
The model understands the textual context, processes it, and generates outputs.
### Code
The model also takes programming language code as input.

## Large Multimodal Model(LMM) Prompting With Google Gemini
I will show you the steps to use the Goolge Gemini multimodal.
### 1. Login to Google Cloud
Since I am demonstrating from the Google Cloud Console, you will need to login to your 
Google Cloud account or need to signup if you don't Google Cloud account.
here is the link [cloud.google.com](https://cloud.google.com). For this demonstration I have set up a branch new account.

### 2. Setup Billing
If you haven't set up billing, you'll need to add your payment details so Google can charge you accordingly.
If you're a new user on the Google Cloud Platform, you'll get a 90-day free trial with $300 USD in credit, which will be more than enough to try out Gemini for our demonstration.

### 3. Create Project 
Go to your Google Cloud Console [https://console.cloud.google.com](https://console.cloud.google.com) 
and then create new google project clicking one **red box 1** which will open red box popup and click on **red box 2**. 

![Create Google Project](/images/posts/large-multimodal-model-prompting-with-google-gemini-and-vertex-ai/create-google-project.png#center "Create Google Project")

Give the name to the Google project form **red box 3**.
You can also see the project ID just below the project name text box; this ID cannot be changed in the future. You can leave the location set to the default. 
![Create Google Project](/images/posts/large-multimodal-model-prompting-with-google-gemini-and-vertex-ai/create-google-project-2.png#center "Create Google Project").
After you hit a save button it will take few seconds to create a project.

### 4. Enable APIs & Services
To enable APIs & Services first you need to go to **red box 4** and click on **red box 5**
![Enable APIs & Services](/images/posts/large-multimodal-model-prompting-with-google-gemini-and-vertex-ai/enable-api-services-1.png#center "Enable APIs & Services")
Now you are inside a project dashboard. Search for "Enable APIs & Services" form search text box  **(Red box 6)**  and click on Enable APIs & Services **(Red box 7)**
![Enable APIs & Services](/images/posts/large-multimodal-model-prompting-with-google-gemini-and-vertex-ai/enable-api-services-2.png#center "Enable APIs & Services")
Click on **+ ENABLE APIS AND SERVICES** (Red Box 8)
![Enable APIs & Services](/images/posts/large-multimodal-model-prompting-with-google-gemini-and-vertex-ai/enable-api-services-3.png#center "Enable APIs & Services")
Search for `Vertex AI API` and click on `Vertex AI API` **(Red Box 9)**
![Vertex AI API](/images/posts/large-multimodal-model-prompting-with-google-gemini-and-vertex-ai/vertex-ai-api-1.png#center "Vertex AI API")
Click on  `Vertex AI API` text **(Red Box 10)**
![Vertex AI API](/images/posts/large-multimodal-model-prompting-with-google-gemini-and-vertex-ai/vertex-ai-api-2.png#center "Vertex AI API")
Click on enable button **(Red Box 11)** You may be asked for billing information if you haven’t submitted it already.
![Vertex AI API](/images/posts/large-multimodal-model-prompting-with-google-gemini-and-vertex-ai/vertex-ai-api-3.png#center "Vertex AI API")
It will take coupe of seconds to enable the `Vertex AI API`.
Once the Vertex API is enabled, your browser window will display the screen shown below.
![Vertex AI API](/images/posts/large-multimodal-model-prompting-with-google-gemini-and-vertex-ai/vertex-ai-api-4.png#center "Vertex AI API")

### 5. Prompting With Multimodal

Now let's go to the Vertex AI studio.

Search for Vertex AI and click on Vertex AI **(Red Box 12)**.
![Vertex AI studio](/images/posts/large-multimodal-model-prompting-with-google-gemini-and-vertex-ai/vertex-ai-studio-1.png#center "Vertex AI studio")
Next step is to clink on `freeform` menu **(Red Box 13)**
![Vertex AI studio](/images/posts/large-multimodal-model-prompting-with-google-gemini-and-vertex-ai/prompting-with-freeform-1.png#center "Vertex AI studio")
From the screen above, you can choose the LMM model **(Red Box 14)**, upload files **(Red Box 15)** and input prompts.

#### Example
I have below text prompt and multimodal inputs (a text file `knowledge-base.txt` and an image `mount-everest.png`).

**Text prompt is :** 
> Read the information from both the text file and the image. Tell us the old and new heights

**Image is : (`mount-everest.png`)** Image has text on it.
![Mount Everest](/images/posts/large-multimodal-model-prompting-with-google-gemini-and-vertex-ai/mount-everest.png#center "Mount Everest")

**Text file is (`knowledge-base.txt`):** text file has below content
```text
The height of Mount Everest was 8,848 meters
```

Let me try with only text prompt, what will be the output?
![Vertex AI studio](/images/posts/large-multimodal-model-prompting-with-google-gemini-and-vertex-ai/prompting-with-freeform-only-with-text-prompt.png#center "Vertex AI studio")

Look at the response. Model did not find the information that we asked for.

**Output is:**
> Please provide me with the text file and the image so I can read the information and tell you the old and new heights.


Let's try with the files (`knowledge-base.txt` text file and `mount-everest.png` image file) and supply same prompt used before.
![prompting with freeform](/images/posts/large-multimodal-model-prompting-with-google-gemini-and-vertex-ai/prompting-with-freeform-only-with-files.png#center "prompting with freeform")

Now, look at the output. 
The model is reading both the `knowledge-base.txt` text file and the `mount-everest.png` image file, and is responding the correct result.

**Output is:**

>The old height of Mount Everest was 8,848 meters. The new height of Mount Everest is 8,849 meters.

Old height of Mount Everest was read from `knowledge-base.txt` text file and new height from the `mount-everest.png` image file, which is the expected output.

**Congratulations!** You have successfully learned how to use Large Multimodal Model (LMM) prompting with Google Gemini and Vertex AI

One more thing: after completing the lab, don't forget to delete your project; otherwise, Google may charge you.

In this post, we explored the capabilities of Large Multimodal Models (LMM) 
and demonstrated how Google Gemini processes different data modalities to generate meaningful outputs. 
By using text and image inputs, we saw how the model effectively interprets and 
combines multimodal data to provide accurate results

**Recorded YouTube video for the demonstration:**

{{< youtube juHbhJzxZ6c >}}