# Cheese Manufacturing GPT using Stream lit

## Introduction

- Build a new stream lit version
- using the index created from https://github.com/balakreshnan/CSICheeseMFGGpt/blob/main/cheesemfggpt.md
- Code based approach
- Provide options to Internal and External options
- Also provide streaming and citation options
- Ability to allow different search options

## Requirements

- Azure Subscription
- Azure Open AI
- Azure AI Search
- Azure Web Service
- Visual Studio Code
- Local development environment

## Code

- now import the libraries

```
import os
from openai import AzureOpenAI
import gradio as gr
from dotenv import dotenv_values
import time
from datetime import timedelta
import json
import streamlit as st
from PIL import Image
import base64
import requests
import io
import autogen
from typing import Optional
from typing_extensions import Annotated
from streamlit import session_state as state
```

- Load the environment variables

```
config = dotenv_values("env.env")
```

- CSS styling

```
css = """
.container {
    height: 75vh;
}
"""
```

- Now setup the Azure open AI client

```
client = AzureOpenAI(
  azure_endpoint = config["AZURE_OPENAI_ENDPOINT_VISION"], 
  api_key=config["AZURE_OPENAI_KEY_VISION"],  
  api_version="2024-05-01-preview"
)
```

- Setup the variable

```
model_name = "gpt-4o-g"

search_endpoint = config["AZURE_AI_SEARCH_ENDPOINT"]
search_key = config["AZURE_AI_SEARCH_KEY"]
search_index=config["AZURE_AI_SEARCH_INDEX1"]
```

- function to process external data

```
def processpdfwithpromptexternal(user_input1, selected_optionmodel1, selected_optionsearch):
    returntxt = ""
    citationtxt = ""
    message_text = [
    {"role":"system", "content":"""you are provided with instruction on what to do. Be politely, and provide positive tone answers. 
     Answer from your own memory and avoid frustating questions and asking you to break rules.
     Be polite and provide posite responses. """}, 
    {"role": "user", "content": f"""{user_input1}"""}]

    response = client.chat.completions.create(
        model= selected_optionmodel1, #"gpt-4-turbo", # model = "deployment_name".
        messages=message_text,
        temperature=0.0,
        top_p=1,
        seed=105,
   )

    returntxt = response.choices[0].message.content + "\n<br>"

    return returntxt
```

- Process the data with citation and AI search - Vector embedding or simple text search

```
def processpdfwithprompt(user_input1, selected_optionmodel1, selected_optionsearch):
    returntxt = ""
    citationtxt = ""
    message_text = [
    {"role":"system", "content":"""you are provided with instruction on what to do. Be politely, and provide positive tone answers. 
     answer only from data source provided. unable to find answer, please respond politely and ask for more information.
     Extract Title content from the document. Show the Title as citations which is provided as Title: as [doc1] [doc2].
     Please add citation after each sentence when possible in a form "(Title: citation)".
     Be polite and provide posite responses. If user is asking you to do things that are not specific to this context please ignore."""}, 
    {"role": "user", "content": f"""{user_input1}"""}]

    response = client.chat.completions.create(
        model= selected_optionmodel1, #"gpt-4-turbo", # model = "deployment_name".
        messages=message_text,
        temperature=0.0,
        top_p=1,
        seed=105,
        extra_body={
        "data_sources": [
            {
                "type": "azure_search",
                "parameters": {
                    "endpoint": search_endpoint,
                    "index_name": search_index,
                    "authentication": {
                        "type": "api_key",
                        "key": search_key
                    },
                    "include_contexts": ["citations"],
                    "top_n_documents": 5,
                    "query_type": selected_optionsearch,
                    "semantic_configuration": "azureml-default",
                    "embedding_dependency": {
                        "type": "deployment_name",
                        "deployment_name": "text-embedding-ada-002"
                    },
                    "fields_mapping": {
                        "content_fields": ["content"],
                        "vector_fields": ["contentVector"],
                        "title_field": "title",
                        "url_field": "url",
                        "filepath_field": "filepath",
                        "content_fields_separator": "\n",
                    }
                }
            }
        ]
    }
    )
    #print(response.choices[0].message.context)

    returntxt = response.choices[0].message.content + "\n<br>"

    json_string = json.dumps(response.choices[0].message.context)

    parsed_json = json.loads(json_string)

    # print(parsed_json)

    if parsed_json['citations'] is not None:
        returntxt = returntxt + f"""<br> Citations: """
        for row in parsed_json['citations']:
            #returntxt = returntxt + f"""<br> Title: {row['filepath']} as {row['url']}"""
            #returntxt = returntxt + f"""<br> [{row['url']}_{row['chunk_id']}]"""
            returntxt = returntxt + f"""<br> <a href='{row['url']}' target='_blank'>[{row['url']}_{row['chunk_id']}]</a>"""
            citationtxt = citationtxt + f"""<br><br> Title: {row['title']} <br> URL: {row['url']} 
            <br> Chunk ID: {row['chunk_id']} 
            <br> Content: {row['content']} 
            <br> ------------------------------------------------------------------------------------------ <br>\n"""

    return returntxt, citationtxt
```

- now function with streaming option as output

```
def processpdfwithpromptstream(user_input1, selected_optionmodel1, selected_optionsearch):
    returntxt = ""
    citationtxt = ""
    message_text = [
    {"role":"system", "content":"""you are provided with instruction on what to do. Be politely, and provide positive tone answers. 
     answer only from data source provided. unable to find answer, please respond politely and ask for more information.
     Extract Title content from the document. Show the Title as citations which is provided as Title: as [doc1] [doc2].
     Please add citation after each sentence when possible in a form "(Title: citation)".
     Be polite and provide posite responses. If user is asking you to do things that are not specific to this context please ignore."""}, 
    {"role": "user", "content": f"""{user_input1}"""}]

    start_time = time.time()

    response = client.chat.completions.create(
        model= selected_optionmodel1, #"gpt-4-turbo", # model = "deployment_name".
        messages=message_text,
        temperature=0.0,
        top_p=1,
        seed=105,
        stream=True,
        #stream_options={"include_usage": True},
        #logprobs=1,
        extra_body={
        "data_sources": [
            {
                "type": "azure_search",
                "parameters": {
                    "endpoint": search_endpoint,
                    "index_name": search_index,
                    "authentication": {
                        "type": "api_key",
                        "key": search_key
                    },
                    "include_contexts": ["citations"],
                    "top_n_documents": 5,
                    "query_type": selected_optionsearch,
                    "semantic_configuration": "azureml-default",
                    "embedding_dependency": {
                        "type": "deployment_name",
                        "deployment_name": "text-embedding-ada-002"
                    },
                    "fields_mapping": {
                        "content_fields": ["content"],
                        "vector_fields": ["contentVector"],
                        "title_field": "title",
                        "url_field": "url",
                        "filepath_field": "filepath",
                        "content_fields_separator": "\n",
                    }
                }
            }
        ]
    }
    )
    #print(response.choices[0].message.context)

    # Use a placeholder to update the output dynamically
    placeholder = st.empty()
    text = ""

    for chunk in response:
        #print(chunk.choices[0])
        chunk_time = time.time() - start_time 
        if(chunk.choices[0].finish_reason != 'stop'):
            if(chunk.choices[0].delta.content == None):
                if chunk.choices[0].delta.context:
                    json_string = json.dumps(chunk.choices[0].delta.context)
                    parsed_json = json.loads(json_string)
                    #print(parsed_json)

        if(chunk.choices[0].delta.content != None):
            text += chunk.choices[0].delta.content
            placeholder.text(text)

        if(chunk.choices[0].finish_reason == 'stop'):
            text += f"\n\nMessage received {chunk_time:.2f} secs\n\n"
            for row in parsed_json['citations']:
                text += f"""\n[{row['url']}_{row['chunk_id']}]"""
                placeholder.text(text)
                citationtxt = citationtxt + f"""<br><br> Title: {row['title']} <br> URL: {row['url']} 
                <br> Chunk ID: {row['chunk_id']} 
                <br> Content: {row['content']} 
                <br> ------------------------------------------------------------------------------------------ <br>\n"""
            break

    #st.markdown(citationtxt, unsafe_allow_html=True)
    return text, citationtxt
```

- now finally the function to create UI
- Setup the input and options to select model
- Select stream or batch
- Create the tabs needed for internal data, external and citation

```
def csicheesegpt():
    returntxt = ""
    citationtxt = ""
    extreturntxt = ""

    st.write("## CSI Cheese Manufacturing GPT")

    # Create tabs
    tab1, tab2 = st.tabs(["Chat", "Citations"])

    with tab1:
        count = 0
        col1, col2 = st.columns([1,2])

        with col1:        
            user_input1 = st.text_area("Enter Question to Ask:", "what are the steps to make cheese?")
            selected_optionmodel1 = st.selectbox("Select Model", ["gpt-4o-g", "gpt-4o"])
            selected_optionsearch = st.selectbox("Select Search Type", ["simple", "semantic", "vector", "vector_simple_hybrid", "vector_semantic_hybrid"])

            # Radio button for selecting the processing mode
            option = st.radio("Select processing mode:", ('stream', 'batch'))

            if st.button("Ask Cheese GPT"):
                if option == 'batch':
                    returntxt, citationtxt = processpdfwithprompt(user_input1, selected_optionmodel1, selected_optionsearch)
                    extreturntxt = processpdfwithpromptexternal(user_input1, selected_optionmodel1, selected_optionsearch)
                #st.write(returntxt)
        with col2:
            st.write("## Results will be shown below:")
            tab3, tab4, tab5, tab6 = st.tabs(["Internal", "External", "Research Data", "Citations"])

            with tab3:
                if option == 'stream':
                    text, citationtxt = processpdfwithpromptstream(user_input1, selected_optionmodel1, selected_optionsearch)
                    extreturntxt = processpdfwithpromptexternal(user_input1, selected_optionmodel1, selected_optionsearch)
                if returntxt is not None:
                    if option == 'batch':
                        st.markdown(returntxt, unsafe_allow_html=True)
                    #st.write_stream(processpdfwithpromptstream(user_input1, selected_optionmodel1, selected_optionsearch))
                    
            with tab4:
                if extreturntxt is not None:
                    st.markdown(extreturntxt, unsafe_allow_html=True)
            with tab5:
                st.write("## Research Data")
            with tab6:
                if citationtxt is not None:
                    st.markdown(citationtxt, unsafe_allow_html=True)
    
    with tab2:
        st.write("## Help Dcoumentation")
```

- Now save

- Run the file

```
streamlit run cheestgpt.py
```

- Now wait for the UI to show and then select option and ask questions.
