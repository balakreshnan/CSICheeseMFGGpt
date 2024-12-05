# CSI Cheese Manufacturing GPT

## Introduction

- Build a GPT to understand Cheese manufacuring regulations and best practises.
- Cheese Manufacturing GPT

[![YouTube Video](https://img.youtube.com/vi/UOaKXG542GM/0.jpg)](https://youtu.be/UOaKXG542GM)

[CheeseGPT](https://csicheesemfggpt.azurewebsites.net/){:target="_blank"}

![Cheese GPT](https://github.com/balakreshnan/CSICheeseMFGGpt/blob/main/csicheesemfggpt1.jpg "Cheese GPT")

## Overview

This document provides a detailed explanation of the architecture depicted in the diagram. The architecture integrates various components to utilize Azure OpenAI, embedding, and AI search capabilities to support Manufacturing (MFG) Plant Floor Users with efficient data retrieval and summarization.
 

## Components and Workflow

1. PDF Documents
Source: The process starts with PDF documents which contain the raw data.
Role: These documents need to be processed and made searchable and understandable by the AI system.
2. Data Scientists
Role: Data Scientists are responsible for preparing and feeding the data into the system. They work on data preprocessing, cleaning, and ensuring the data is suitable for AI consumption.
3. Azure OpenAI Studio
Role: This is the core component where the magic happens.
Embedding: The PDF documents are processed to generate embeddings. Embeddings are numerical representations of the documents' content, which can be used for various downstream tasks like search and summarization.
Summarization: The embeddings can also be used to summarize documents, providing concise and relevant information.
4. AI Search
Role: AI Search utilizes the embeddings generated by Azure OpenAI Studio to enable efficient search functionality. Users can query the system, and it will return relevant results based on the embeddings.
5. App Services
Role: App Services act as an intermediary layer that facilitates the communication between the various components.
Integration: It integrates the AI search results and the summarization functionality to provide a seamless experience for the end-users.
APIs: It exposes APIs that can be consumed by different user interfaces or applications.
6. MFG Plant Floor Users
Role: These are the end-users who utilize the system to retrieve information from the PDF documents. They benefit from the search and summarization functionalities provided by the system.
 

## Data Flow
 
### PDF Document Ingestion
PDF documents are ingested into the system, which are then processed by Data Scientists to ensure they are clean and suitable for AI processing.

### Embedding Generation
The processed PDF documents are sent to Azure OpenAI Studio where embeddings are generated. This numerical representation of the documents is crucial for search and summarization tasks.

### Search and Summarization
The embeddings are utilized by the AI Search component to enable efficient document search.
Simultaneously, embeddings can be used for summarizing the document content for quick insights.

### App Services Integration
The search results and summaries are integrated by App Services, which ensures that the information is presented in a user-friendly manner to the MFG Plant Floor Users.
User Interaction
MFG Plant Floor Users interact with the system via user interfaces or applications that consume the APIs exposed by App Services. They can search for specific information and receive summarized content as needed.
 

## RAG Vectorization and Consumer
 
RAG Vectorization: This refers to the process of converting raw text data from PDFs into vectorized representations (embeddings). This step is crucial for enabling the AI search and summarization functionalities.
RAG Consumer: This indicates the consumption of the vectorized data by the end-users, i.e., MFG Plant Floor Users, through the functionalities provided by the App Services.
 

## Conclusion
 
This architecture leverages the capabilities of Azure OpenAI Studio for embedding and summarization, combined with AI search and App Services, to provide an efficient and user-friendly system for MFG Plant Floor Users. The integration ensures that users can easily search and retrieve relevant information from a large corpus of PDF documents, enhancing productivity and decision-making.

![Cheese GPT](https://github.com/balakreshnan/CSICheeseMFGGpt/blob/main/images/cheesgpt1.jpg "Cheese GPT")

![Cheese GPT](https://github.com/balakreshnan/CSICheeseMFGGpt/blob/main/images/cheesgpt2.jpg "Cheese GPT")

![Cheese GPT](https://github.com/balakreshnan/CSICheeseMFGGpt/blob/main/images/cheesgpt3.jpg "Cheese GPT")

![Cheese GPT](https://github.com/balakreshnan/CSICheeseMFGGpt/blob/main/images/cheesgpt4.jpg "Cheese GPT")
![Cheese GPT](https://github.com/balakreshnan/CSICheeseMFGGpt/blob/main/images/cheesgpt5.jpg "Cheese GPT")

## sample questions to ask

- what are top 10 actions steps for food safety systems to raw milk cheese production? 
- show me details on heat treatment? 
- what are the somatic cell counts specification? 
- what are the hazards condition in can cooling systems?
- what are the cybersecurity configuration to keep the OT layer secured?
- what PPE to use when cleaning cheese lines?
- What if there is hazardous chemicals used?
