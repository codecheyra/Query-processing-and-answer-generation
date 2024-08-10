# Query-processing-and-answer-generation

## Index
1. [Initial Arrangements](#initial-arrangements)
2. [Install Necessary Packages](#install-necessary-packages)
3. [Start Ollama](#start-ollama)
4. [Define Settings and Load Documents](#define-settings-and-load-documents)
5. [Main Function to Handle Input and Query Processing](#main-function-to-handle-input-and-query-processing)
6. [Example Usage](#example-usage)
7. [Additional Tips](#additional-tips)

## Initial Arrangements

### Upgrade Python3
Ensure that Python3 is upgraded to the latest version. You can check your Python version using:

```bash
python3 --version
```

If you need to upgrade Python, follow the instructions for your operating system.

### Create Project Directory
Create a directory for your project:

```bash
mkdir project_directory
cd project_directory
```

### Open Project Folder
Open the project folder in your preferred code editor.

### Create and Activate Virtual Environment
Create a virtual environment and activate it:

```bash
python3 -m venv venv
source venv/bin/activate  # On Windows use `venv\Scripts\activate`
```

## Install Necessary Packages

### Install Packages in Jupyter Notebook
Open your Jupyter Notebook and run the following commands to install the required packages:

```python
!pip install llama-index-llms-ollama
!pip install llama-index-embeddings-huggingface
!pip install llama-index
!pip install langchain
!pip install transformers
!pip install huggingface_hub
!pip install --upgrade llama-index
!pip install --upgrade langchain
!pip install --upgrade transformers
!pip install --upgrade huggingface_hub
!pip install --upgrade llama-index-llms-ollama llama-index-embeddings-huggingface
```

## Start Ollama
To start Ollama, open a terminal and run:

```bash
ollama start
```

If you encounter a port error, use the following commands:

```bash
killall ollama
ollama start
```

Then, open another terminal and run:

```bash
ollama run llama3
```

## Define Settings and Load Documents

### Define Settings

```python
import json
import logging
from llama_index.core import VectorStoreIndex, SimpleDirectoryReader, Settings
from llama_index.embeddings.huggingface import HuggingFaceEmbedding
from llama_index.llms.ollama import Ollama
from llama_index.readers.file import PDFReader
```

### Load Documents

```python
# Read input JSON file to get the PDF path
with open('input.json', 'r') as file:
    input_data = json.load(file)
    pdf_directory = input_data['pdf_directory']

# PDF Reader with SimpleDirectoryReader
parser = PDFReader()
file_extractor = {".pdf": parser}
documents = SimpleDirectoryReader(pdf_directory, file_extractor=file_extractor).load_data()

# bge-base embedding model
Settings.embed_model = HuggingFaceEmbedding(model_name="BAAI/bge-base-en-v1.5")

# ollama
logging.basicConfig(level=logging.WARNING)
logging.getLogger('llama_index').setLevel(logging.ERROR)
logging.getLogger('httpx').setLevel(logging.ERROR)
logging.getLogger('httpcore').setLevel(logging.ERROR)

Settings.llm = Ollama(model="llama3", request_timeout=360.0)
index = VectorStoreIndex.from_documents(documents)
```

## Main Function to Handle Input and Query Processing

```python
query_engine = index.as_query_engine()
question = ""    # your question here
print("\nQuestion: ", question)

try:
    response = query_engine.query(question)
    response_text = str(response)
    print("\nAnswer: ", response_text)
    
    # Write the output to a JSON file
    output_data = {"question": question, "answer": response_text}
    with open('output.json', 'w') as outfile:
        json.dump(output_data, outfile, indent=4)
except Exception as e:
    print(f"Error occurred: {e}")
    # Write the error to the output JSON file
    error_data = {"error": str(e)}
    with open('output.json', 'w') as outfile:
        json.dump(error_data, outfile, indent=4)
```

## Example Usage

### Run the setup
Ensure your directory structure is as follows:

```
project_directory/
├── qanda/
│   └── sample.pdf
├── input.json
├── output.json
└── main.py
```

### Contents of `input.json`:
```json
{
  "pdf_directory": "qanda/"
}
```

### Run the script `main.py`:
```bash
python3 main.py
```

## Additional Tips
- Ensure your virtual environment is activated before running the notebook.
- Keep your packages up to date by regularly using `pip install --upgrade <package-name>`.
