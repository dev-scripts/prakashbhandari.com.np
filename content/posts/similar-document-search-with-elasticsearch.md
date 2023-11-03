---
title: "Similar Document Search With Elasticsearch"
date: 2023-11-03T08:06:33+11:00
draft: false
showToc: true
TocOpen: true
tags : [
  "Elasticsearch",
  "Recommendation",
]
categories : [
  "Elasticsearch",
]
keywords: [
  "Similar Document Search With Elasticsearch",
  "k-nearest neighbor (kNN) search",
  "Elasticsearch More Like This query",
  "How to create elasticsearch index",
  "content-based recommendation systems",
  "Elasticsearch document searches",
  "Similar document searches",
  "Content similarity",
  "'more_like_this' query",
  "Min_term_freq",
  "Max_query_terms",
  "Min_doc_freq",
  "Fine-tuning similarity search",
  "Content similarity in Elasticsearch",
  "Elasticsearch content recommendation",
  "Elasticsearch product recommendation",
  "Elasticsearch news article recommendation",
]
cover:
  image: "images/posts/similar-document-search-with-elasticsearch/elastic-search.webp" 
  alt: "Similar Document Search With Elasticsearch"
  caption: "Similar Document Search With Elasticsearch"
  relative: false
author: "Prakash Bhandari"
description: "In this article, we explored the concept of performing similar document searches with Elasticsearch, with a focus on content similarity using the 'more_like_this' query. We discussed the key parameters like min_term_freq, max_query_terms, and min_doc_freq that play a crucial role in fine-tuning the similarity search."
---


On various news portals and e-commerce platforms, you may have seen recommendations for articles and products related to the main article or product.

Recommending  similar products, articles, or documents involves complex algorithms, but Elasticsearch empowers us to utilize recommendation algorithms effortlessly.

Mainly, recommending similar products, articles, or documents is accomplished through content similarity or by considering the user's query history.

I will provide an example of Amazon product recommendations. In the image below, "Get similar item Fast" is related to content similarity, while the second one, "Customers who viewed this item also viewed" is related to the user's query history.

![Amazon product recommendations](/images/posts/similar-document-search-with-elasticsearch/amazon-recommendation.png#center)

# Setup Elasticsearch locally

Before diving deep into finding related document, let's set up the Elasticsearch project on your machine. I won't go into detail on how to set up Elasticsearch locally; 
instead, I will clone the project from GitHub, which I have already created. 

You can also clone it from my GitHub public repo. [dockerize-elasticsearch](https://github.com/dev-scripts/dockerize-elasticsearch)

Or you can just use the below `docker-compose.yml` file to run elasticsearch and kibana locally
```dockerfile
version: '3.7'
services:
  elasticsearch:
    image: 'docker.elastic.co/elasticsearch/elasticsearch:8.10.2'
    container_name: elasticsearch
    volumes:
      - 'data_es:/usr/share/elasticsearch/data'
    environment:
      - discovery.type=single-node
      - CLI_JAVA_OPTS=-Xms2g -Xmx2g
      - bootstrap.memory_lock=true
      - xpack.security.enabled=false
      - xpack.security.enrollment.enabled=false
    ports:
      - '9300:9200'
    networks:
      - esnet
  kibana:
    image: 'docker.elastic.co/kibana/kibana:8.10.2'
    container_name: kibina
    environment:
      - 'ELASTICSEARCH_HOSTS=http://elasticsearch:9200'
      - 'XPACK_ENCRYPTEDSAVEDOBJECTS_ENCRYPTIONKEY=d1a66dfd-c4d3-4a0a-8290-2abcb83ab3aa'
    ports:
      - '5701:5601'
    networks:
      - esnet
volumes:
  data_es:
    driver: local
networks:
  esnet: null
```

Once your Elasticsearch and Kibana project is up you can browse Kibana via `http://localhost:5701/`

![Kibana](/images/posts/similar-document-search-with-elasticsearch/kibina.png#center)

**Kibana** is an open source analytics and visualization platform designed to work with Elasticsearch.


# Similar Document Search With Elasticsearch
In this post, I will focus on content similarity with an example in Elasticsearch.

In Elasticsearch, content similarity can be found in two ways:

1. k-nearest neighbor (kNN) search.
2. More-like-this query.

## k-nearest neighbor (kNN) search

A k-nearest neighbor (kNN) search finds the k nearest vectors to a query vector, as measured by a similarity metric.

Common use cases for kNN include:

Relevance ranking based on natural language processing (NLP) algorithms
Product recommendations and recommendation engines
Similarity search for images or videos

## More-like-this query

The More Like This(MLT) Query allows you to find the similar documents
from an input.

It works from a new query built from the relevant terms present in the input.


In this post, I will be focusing on *More-like-this query*. I will write new blog post for *k-nearest neighbor (kNN) search*


You can find the full Elasticsearch documentation at [elastic.co](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-mlt-query.html)
for input parameters, term selection parameters, and query formation parameters. I will not delve into the details of these aspects.

In this post, I will demonstrate product reviews for Apple and recommend similar reviews.

For this demo I will use `like` Document Input Parameters and 
`min_term_freq`, `max_query_terms`, and  `min_doc_freq` Term Selection Parameters.

**min_term_freq:** This sets the minimum term frequency, below which terms will be ignored from the input document. 
In our case, `min_term_freq` is set to `2`, indicating that the main document must have a term occurring two or more times.



**max_query_terms:** This  defines the maximum number of query terms that will be included in the generated query. 
It imposes a limit on the number of terms in the query. 
In our case, max_query_terms is set to **12**, which means that if a documents contains more than **12** terms, it will not be considered related document and ignored.


**min_doc_freq:** This specifies the minimum document frequency that a term must have to be considered when generating a query. 
Document frequency refers to the number of documents in which a term appears. 
Terms with a low document frequency are considered less common and specific,
whereas terms with a high document frequency are more common and general. 
In our case, `min_doc_freq` is set to **5**,
indicating that a term should be present in at least in **5** documents.
If a term's document frequency is less than **5**, it will not yield any results.

### Create Index with config map
You can use the create index API to add a new index to an Elasticsearch cluster. When creating an index, you can specify the following:

1. Settings for the index
2. Mappings for fields in the index
3. Index aliases

The create index API allows for providing a mapping definition. 
For our demo purpose I am creating `apple-review-index` index with `review` text field.  
In `review` I will be storing review of each user for Apple's product

```
PUT /apple-review-index
{
  "mappings": {
    "properties": {
      "review": { "type": "text" }
    }
  }
}
```
Once index is successfully created, you will be able to see 200 response code with below response
```
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "index": "apple-review-index"
}
```
Result of create index is attached blow.
![Create elasticsearch index](/images/posts/similar-document-search-with-elasticsearch/create-index.png#center)


### Store date in Field
We have already created the index with `review` field. Now in the `apple-review-index` we have `review` field.
In `apple-review-index`  I will be storing below 7 reviews.

```
"Love apple AirPods, the sound and quality are amazing as always with Apple"
"Bought to use on iPad and iPhone, perfect item again from Apple, does everything is should."
"Again Great product form Apple, fast delivery and very good AirPods."
"Can't go wrong with good quality Apple AirPods. Sound quality is good, long lasting battery, quality of the product is good!"
"Really quick delivery and great value. Apple product works perfectly"
"Products are expensive "
"Nice packaging."
```
Let's store all the reviews one by one.

```
POST /apple-review-index/_doc
{
    "review" : "Love apple AirPods, the sound and quality are amazing as always with Apple"
}
```
Once record  is successfully created in the index, you will be able to see 200 response code with below response
```
{
  "_index": "apple-review-index",
  "_id": "7DHbkosBIYeptuTSNkzy",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "_seq_no": 0,
  "_primary_term": 1
}
```
The create index screenshot is attached below.
![Create elasticsearch index](/images/posts/similar-document-search-with-elasticsearch/insert.png#center)

### Retrieve all the data form index

We have already inserted all 7 reviews in the `apple-review-index`. 
Let's retrieve all of them to confirm whether all the reviews are in the Elasticsearch index.

```
GET /apple-review-index/_search
{
  "query": {
    "match_all": {} 
  }
}
```
You will see response like below. 
Where you will be able to see all the reviews that we have created before.
```
{
  "took": 17,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 7,
      "relation": "eq"
    },
    "max_score": 1,
    "hits": [
      {
        "_index": "apple-review-index",
        "_id": "7DHbkosBIYeptuTSNkzy",
        "_score": 1,
        "_source": {
          "review": "Love apple AirPods, the sound and quality are amazing as always with Apple"
        }
      },
      {
        "_index": "apple-review-index",
        "_id": "7THjkosBIYeptuTSu0xS",
        "_score": 1,
        "_source": {
          "review": "Bought to use on iPad and iPhone, perfect item again from Apple, does everything is should."
        }
      },
      {
        "_index": "apple-review-index",
        "_id": "7jHjkosBIYeptuTS_Ey2",
        "_score": 1,
        "_source": {
          "review": "Again Great product form Apple, fast delivery and very good AirPods."
        }
      },
      {
        "_index": "apple-review-index",
        "_id": "7zHkkosBIYeptuTSPExg",
        "_score": 1,
        "_source": {
          "review": "Can't go wrong with good quality Apple AirPods. Sound quality is good, long lasting battery, quality of the product is good!"
        }
      },
      {
        "_index": "apple-review-index",
        "_id": "8DHkkosBIYeptuTSfUxk",
        "_score": 1,
        "_source": {
          "review": "Really quick delivery and great value. Apple product works perfectly"
        }
      },
      {
        "_index": "apple-review-index",
        "_id": "8THkkosBIYeptuTSuUw1",
        "_score": 1,
        "_source": {
          "review": "Products are expensive"
        }
      },
      {
        "_index": "apple-review-index",
        "_id": "8jHlkosBIYeptuTSXUzC",
        "_score": 1,
        "_source": {
          "review": "Nice packaging."
        }
      }
    ]
  }
}
```

The search results in the index screenshot is attached below.

![Search](/images/posts/similar-document-search-with-elasticsearch/search.png#center)

### Apply more_like_this Query
Certainly, let's proceed with finding similar reviews in the `apple-review-index` by applying a "more_like_this" query.
To do this, you'll need to provide the actual review content that you want to use as a source for finding similar reviews. 

Let's create a query for Elasticsearch using the "more_like_this" query. Here are the details:

1. **Actual review content:**  "Love apple AirPods, the sound and quality are amazing as always with Apple"
2. **min_term_freq** : 2 (indicating that a term in the actual review content must occur two or more times)
3. **max_query_terms** : 12 (indicating that a term in the actual review content must not occur more than 12 times)
4. **max_doc_freq**: 5 (indicating that a term should appear in a maximum of 5 reviews)

In our actual review content, the term "Apple" appears twice.

Here is the similar review search query

```
GET /apple-review-index/_search
{
  "query": {
    "more_like_this" : {
      "fields" : ["review"],
      "like" : "Love apple AirPods, the sound and quality are amazing as always with Apple",
      "min_term_freq" : 2,
      "max_query_terms" : 12,
      "max_doc_freq": 5
    }
  }
}

```

The query should only return the 5 reviews, while the two reviews below should be ignored. These reviews do not have the term "Apple" in their content.
```
"Products are expensive "
"Nice packaging."
```
Let's see the response 

```
{
  "took": 26,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 5,
      "relation": "eq"
    },
    "max_score": 0.48810866,
    "hits": [
      {
        "_index": "apple-review-index",
        "_id": "7DHbkosBIYeptuTSNkzy",
        "_score": 0.48810866,
        "_source": {
          "review": "Love apple AirPods, the sound and quality are amazing as always with Apple"
        }
      },
      {
        "_index": "apple-review-index",
        "_id": "8DHkkosBIYeptuTSfUxk",
        "_score": 0.38719866,
        "_source": {
          "review": "Really quick delivery and great value. Apple product works perfectly"
        }
      },
      {
        "_index": "apple-review-index",
        "_id": "7jHjkosBIYeptuTS_Ey2",
        "_score": 0.3726874,
        "_source": {
          "review": "Again Great product form Apple, fast delivery and very good AirPods."
        }
      },
      {
        "_index": "apple-review-index",
        "_id": "7THjkosBIYeptuTSu0xS",
        "_score": 0.31387144,
        "_source": {
          "review": "Bought to use on iPad and iPhone, perfect item again from Apple, does everything is should."
        }
      },
      {
        "_index": "apple-review-index",
        "_id": "7zHkkosBIYeptuTSPExg",
        "_score": 0.27108932,
        "_source": {
          "review": "Can't go wrong with good quality Apple AirPods. Sound quality is good, long lasting battery, quality of the product is good!"
        }
      }
    ]
  }
}
```

In the above search response, we have observed only five hits, which is as expected. :)

The search results for similar reviews from the index screenshot is attached below.

![More like this Search](/images/posts/similar-document-search-with-elasticsearch/more-like-this.png#center)

# Conclusion
In conclusion, this post has provided an overview of performing similar document searches with Elasticsearch, focusing on content similarity using the "more_like_this" query. Elasticsearch offers powerful features for finding related documents based on the content of the documents. We have explored the use of various parameters such as min_term_freq, max_query_terms, and min_doc_freq to fine-tune our similarity search.

By creating an index, storing data in fields, and applying the "more_like_this" query, we were able to effectively find similar reviews in our Elasticsearch index. Elasticsearch is a versatile tool that can be used for a wide range of applications, including recommendation systems, content similarity analysis, and more.

This post serves as a starting point for those looking to implement content-based recommendation systems and leverage Elasticsearch's capabilities for similar document searches. Further exploration of Elasticsearch's capabilities and parameter tuning can lead to more refined and accurate results in real-world applications.