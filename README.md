# Ragas RAG Metric Demos

Small, runnable examples of evaluating retrieval-augmented generation (RAG) outputs with [Ragas](https://docs.ragas.io/). The examples use either a local Ollama model through its OpenAI-compatible API or Azure OpenAI.

## Requirements

- Python 3.10 or later
- Either [uv](https://docs.astral.sh/uv/) or pip
- For local examples: [Ollama](https://ollama.com/) running on `http://localhost:11434`
- For Azure examples: an Azure OpenAI resource, a deployed model, and its endpoint and API key

## Install

Using uv:

```powershell
uv sync
```

Using pip:

```powershell
python -m pip install .
```

## Choose and run a demo

Run commands from the repository root. With uv, prefix each command with `uv run`; with pip, use `python` directly.

| Script | Metric | Model provider |
| --- | --- | --- |
| `context_entity_recall_demo.py` | Context Entity Recall | Ollama (`llama3.1-8k`) |
| `context_precision_demo.py` | Context Precision | Ollama (`llama3.2`) |
| `context_recall_demo.py` | Context Recall | Ollama (`qwen2.5:7b`) |
| `context_utilization_demo.py` | Context Utilization | Ollama (`llama3.2:latest`) |
| `nvidia_answer_relevancy.py` | Answer Accuracy | Ollama (`llama3.2:latest`) |
| `nvidia_context_relevance.py` | Context Relevance | Ollama (`llama3.2:latest`) |
| `faithfullness_demo.py` | Faithfulness | Azure OpenAI (`gpt-4o-mini`) |
| `noise_sensitivity_demo.py` | Noise Sensitivity | Azure OpenAI (configured deployment) |
| `nvidia_response_groundness.py` | Response Groundedness | Azure OpenAI (configured deployment) |

For example:

```powershell
uv run python context_precision_demo.py
```

Each script evaluates a small hard-coded example and prints a score. The examples are independent; run only the one you want to try.

## Local Ollama setup

Start Ollama and pull the models used by the demos you intend to run. For example:

```powershell
ollama pull llama3.1:8b
ollama pull llama3.2
ollama pull qwen2.5:7b
```

The entity recall demo expects a custom `llama3.1-8k` model. From the repository root, create it from the included [Modelfile](Modelfile):

```powershell
ollama create llama3.1-8k -f Modelfile
```

The local scripts connect to Ollama's OpenAI-compatible endpoint at `http://localhost:11434/v1`. They pass `ollama` as a placeholder API key because the OpenAI client requires a value; it is not a real credential.

## Azure OpenAI setup

Set the environment variables in the shell where the scripts will run. Do not put real credentials in source files, README examples, or committed `.env` files.

```powershell
$env:AZURE_OPENAI_API_KEY = "<your-key>"
$env:AZURE_OPENAI_ENDPOINT = "https://<your-resource>.openai.azure.com/"
$env:AZURE_OPENAI_DEPLOYMENT = "<your-deployment-name>"
# Optional; defaults to 2024-10-21
$env:AZURE_OPENAI_API_VERSION = "2024-10-21"
```

`noise_sensitivity_demo.py` and `nvidia_response_groundness.py` require the key, endpoint, and deployment variables. `faithfullness_demo.py` requires the key and endpoint and uses `gpt-4o-mini` as its deployment name; change that value in the script if your Azure deployment has a different name. Confirm the deployment name and API version are supported by your Azure resource.

## Dependencies

The direct runtime dependencies are Ragas 0.3.x and the OpenAI Python client. Ragas supplies the metric implementations and its required transitive packages; the demos do not directly import LangChain packages.

## Public repository security

- Keep API keys and other credentials in environment variables or a secret manager, never in committed files.
- `.env` files and local virtual environments are excluded by `.gitignore`; do not commit real values in example files.
- Before publishing, inspect `git diff --cached` and scan the staged files for keys, tokens, passwords, and private endpoints. Ignore rules do not remove secrets already committed or staged.
- If a real credential was exposed, revoke or rotate it; deleting it from the latest file is not sufficient to remove it from Git history.
