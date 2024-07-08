# CSI Cheese manufacturing GPT

## Introduction

- Create a new Cheese manufacturing GPT
- Documents are stored in foodtechgptscheese folder
- Using RAG pattern
- Using GPT 40 model
- Using Talk to your data feature in Azure Open AI

## Requirements

- Azure Subscription
- Azure open AI
- Azure Resource Group
- Azure GPT 4o model
- Azure AI Search
- Azure Web Service

## Steps

- Go to Azure Open AI Studio
- Select Add your data
- Make sure you have the pdf's files downloaded
- in Add your data select upload files
- Then create a AI Search to create vector index
- Need ada model for embeddings
- Follow the steps to create the index.
- Wait for the index to complete
- Now select the data source.
- Adjust the system prompt to only answer from the data source provided
- Add All the jail break and safety instruction in the prompt
- On the right top there is a deploy button.
- Click deploy and provide the details like name and resource group and region.
- Follow the steps to create the web app.
- Once the web app is created, then you should be able to web service and click Browse to access the site
- To change the title and text, add the necessary UI environment variables.
- Here is the front end UI - https://github.com/balakreshnan/sample-app-aoai-chatGPT
- can take a fork and update the UI to fit your needs.
