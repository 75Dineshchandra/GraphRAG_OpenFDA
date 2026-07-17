# GraphRAG OpenFDA

This repository contains a GraphRAG-style workflow for exploring an OpenFDA drug-label knowledge graph using Python, NetworkX, Ollama, and embedding-based retrieval.

The project is designed to help you:
- load a prebuilt OpenFDA graph from disk,
- inspect the graph schema and node/edge structure,
- generate or use embeddings for nodes,
- build context around a drug node,
- query an LLM through Ollama using that context.

Because the graph artifacts are too large to store directly in this repository, the required data files are expected to be downloaded from the shared Google Drive folder below.

Google Drive data link:
https://drive.google.com/drive/folders/1C0KmSjRvNmOW-PvxzUiedcediCZHdZAl?usp=sharing

---

## Overview

The workflow uses a graph of drug-related information and connects it with language model prompting. In practice, this means:
1. loading the graph file,
2. locating a drug node,
3. collecting its related sections and brand information,
4. building a prompt from that context,
5. sending it to an Ollama model such as llama3.

This is especially useful for answering questions about FDA drug label content in a grounded way.

---

## Repository contents

This folder currently contains:

- [README.md](README.md) — project documentation.
- [requirements.txt](requirements.txt) — Python dependencies for running the project.

The notebook and large data files are expected to be provided separately by the viewer or downloaded from the shared Google Drive link above.

---

## Required data files

The workflow expects the following files to be present in the project data folder:
- [../../Rag_files/graph_rag/graph_with_embeddings.pkl](../../Rag_files/graph_rag/graph_with_embeddings.pkl)
- [../../Rag_files/graph_rag/node_id_map.pkl](../../Rag_files/graph_rag/node_id_map.pkl)
- [../../Rag_files/graph_rag/embeddings_checkpoint.pkl](../../Rag_files/graph_rag/embeddings_checkpoint.pkl)

If these files are missing, download them from the Google Drive link above and place them into the data folder used by the project.

For viewers who are following the Colab workflow, the notebook environment already includes the necessary runtime context; they only need to download the data files and run the notebook cells.

---

## Software requirements

### Python
Use Python 3.9 or newer.

### Python packages
Install the Python dependencies from the repository requirements file:

```powershell
python -m pip install -r requirements.txt
```

### Ollama
The LLM part of the workflow depends on Ollama being installed and running locally.

If you are using the Colab version, this part is already handled in the notebook environment and you do not need to install anything extra beyond the provided runtime setup.

For a local machine, install Ollama from:
https://ollama.com/download/windows

After installation, verify it works:

```powershell
ollama --version
```

If the command is not found, add the Ollama installation path to your PATH. On many systems it is:

```text
C:\Program Files\Ollama
```

Start the Ollama server:

```powershell
ollama serve
```

Pull the required models:

```powershell
ollama pull llama3
ollama pull nomic-embed-text
```

---

## Windows setup

From the project folder, create and activate a virtual environment if you want an isolated setup:

```powershell
cd S:\Internships_and_Projects\projects\Graph_rag\github_files\GraphRAG_OpenFDA
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

If you are using the notebook, start Jupyter from the same environment:

```powershell
python -m pip install notebook jupyterlab
jupyter lab
```

---

## Running the workflow

### Option 1: Use the notebook
Open the notebook file provided separately in Jupyter or Colab and run the cells in order.

If you are following the Colab version, simply open the notebook there, upload or mount the downloaded data files, and run the cells in sequence.

### Option 2: Use the Python workflow from the project root
If you have a local Python workflow file in your own setup, run it from the project root after installing the dependencies.

```powershell
python your_workflow_script.py
```

If you do not have a separate workflow script, use the notebook workflow instead.

---

## Typical usage flow

1. Load the graph file.
2. Inspect the graph schema.
3. Identify a drug node.
4. Build a context object from the node’s nearby information.
5. Build a prompt from that context.
6. Call Ollama with the prompt.

A typical question could be:

```text
What should I avoid if I am pregnant and using lidocaine?
```

The workflow will try to answer using the graph-derived context and the LLM response.

---

## Troubleshooting

### Ollama connection error
If you see an error like:

```text
Failed to connect to Ollama
```

then one of the following is true:
- Ollama is not installed,
- Ollama is not running,
- the server is not reachable at localhost:11434,
- the required model has not been downloaded.

Fix it by running:

```powershell
ollama serve
ollama pull llama3
ollama pull nomic-embed-text
```

### Graph file not found
If the script cannot locate the graph file, verify that the data folder exists and contains the expected `.pkl` files.

### Notebook kernel issues
If the notebook fails to start, make sure the correct Python environment is selected in Jupyter.

---

## Notes

- This project depends on a prebuilt graph file and related artifacts.
- The graph is large, so the workflow may take time to load and inspect.
- If you are using a different machine or a fresh environment, you must install the Python dependencies and ensure the data files are present before running the workflow.
- For Colab users, the notebook environment already provides the runtime setup; the main requirement is to place the downloaded data files in the expected folder and run the cells.

---

## Recommended next steps

- Open the notebook and run the cells step by step.
- Verify the graph loads correctly.
- Confirm that Ollama is running before asking LLM-backed questions.
- Adjust the target drug node or query to explore different sections of the graph.
