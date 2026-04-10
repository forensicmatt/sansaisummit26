## Requirement #1
Make sure you have a GitHub account! You will need one if you are running your labs inside of GitHub Codespace.
Running the labs inside Codespace ensures that you do not need any tool setup (but it also means you cannot
run the workshops using a local LLM). 

**Please bring your own OpenAI API key or Google AI Studio API key for the workshop.**

If you want to run the notebooks locally, you will need the following required tools:

## Required tools IF running locally
If you wish to work on this workshop on your own machine, you will need to install the following tools. Please
note that you can also run all the Jupyter Notebook labs inside of codespace if you do not want to run locally.

| Tool                  | URL                                                                 | Required | Note |
|-----------------------|---------------------------------------------------------------------|----------|------|
| Python 3.11+          | [Download](https://www.python.org/downloads)                       | Yes      | Workshop uses Python Jupyter Notebooks. |
| Git                   | [Download](https://git-scm.com/install)                            | Yes      | Used to clone the required GitHub repository for this workshop. |
| Docker Desktop        | [Download](https://docs.docker.com/desktop/)                       | No      | Helpful if you want to use a dev container that is fully setup. |
| VS Code               | [Download](https://code.visualstudio.com/Download)                 | Yes      | Used to view Notebooks and run notebooks. |
| Dev Containers Extension | [Install](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) | No | Useful if using a docker container to setup a dev container. |
| Python Extension      | [Install](https://marketplace.visualstudio.com/items?itemName=ms-python.python) | Yes | For using Python within VSCode. |
| Jupyter Extension     | [Install](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter) | Yes | For using Python within VSCode. |
| Ollama                | [Download](https://ollama.com/download)                            | No      | Required if you want to run the notebooks using a local LLM rather than using an LLM service with an API key. |

## Prepping local llm
Make sure Ollama is installed. Using the ollama cli tool, run the following commands to ensure you have the
modals we use downloaded:

  - `ollama pull embeddinggemma`
  - `ollama pull gemma2`
