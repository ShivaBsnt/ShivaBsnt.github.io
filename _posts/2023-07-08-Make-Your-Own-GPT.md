---
title: Make your own private GPT with python
categories: DataStructure Algorithm
tags:
  - Python

header:
  teaser: "/assets/images/2023-07-08-Make-Your-Own-GPT/private_gpt.png"
---

![Cover Page](/assets/images/2023-07-08-Make-Your-Own-GPT/private_gpt.png)

Although **ChatGPT** is incredible, its understanding is limited to the data used for training.

**Wouldn't it be amazing if we could interact with our own private documents using the power of Large Language Models (LLMs)** without posting them online?

The good news is that we can get started right away! Let me demonstrate.

An open source project called **privateGPT** enables you to parse your own documents and communicate with them using LLM. The LLM will use your materials to produce answers to any queries you have.

(_Note: To use the code, we must have **Python3.10** or later installed. Earlier versions of python will not compile._)

# 1. Clone the repository
Navigate to folder of your choice and clone the repository [https://github.com/imartinez/privateGPT.git](https://github.com/imartinez/privateGPT.git)
. That will create a folder called **"privateGPT"**, so change the working directory (**cd privateGPT**).

    git clone https://github.com/imartinez/privateGPT.git
    cd privateGPT

# 2. Create and activate environment
Create virtual environment using the command (**virtualenv venv**) and activate the environment.

    # creates virtual environment
    virtualenv venv

    # activates the environment

    # for mac and linux
    source venv/bin/activate

    # for windows
    call venv/scripts/activate

# 3. Install packages
Install all the required package listed in **requirements.txt**

    pip install -r requirements.txt

Use **pip freeze** command to check if the package has been installed in the virtual environment.

      pip freeze

# 4. Download LLM model
Download the file **[ggml-gpt4all-j-v1.3-groovy.bin](https://gpt4all.io/models/ggml-gpt4all-j-v1.3-groovy.bin)** (3.5GB in size).

Then, create a subfolder called **"models"** and move the downloaded file to **"models"** folder.

![add model](/assets/images/2023-07-08-Make-Your-Own-GPT/model.png)

# 5. Copy environment (example.env)
There is a file called **"example.env"** in the "privateGPT" folder. Copy that file into new file named **".env"**

    cp example.env .env

![copy .env](/assets/images/2023-07-08-Make-Your-Own-GPT/copy_env.png)


# 6. Add documents
Now add your private documents inside **"source_documents"** folder.
![add documents](/assets/images/2023-07-08-Make-Your-Own-GPT/add_documents.png)

# 7. Ingest documents
Now run the command **python ingest.py**. It will parse your source documents and can take some few times to get completed.

    python ingest.py

# 8. Interact with documents
Run the command **python pivateGPT.py** and start querying your documents.
We can see a prompt with "Enter a query" once it is loaded and we are ready to ask question.

    python pivateGPT.py

![start query](/assets/images/2023-07-08-Make-Your-Own-GPT/query.png)