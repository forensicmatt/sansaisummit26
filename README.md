# API Keys
Please bring your own OpenAI API key or Google AI Studio API key for the workshop.

# Running via GitHub Codespace
GitHub Codespaces lets you run this project in a fully configured cloud environment directly in your browser — no local setup required.
Once the labs are made available, you can open this repo in Codespace as follows:

## Steps
1. Navigate to the repository on GitHub.
2. Click the green **`<> Code`** button near the top-right of the repository page.
3. Select the **Codespaces** tab.
4. Click **"Create codespace on main"** (or your desired branch).
5. Wait for the Codespace to build and open — this may take a minute or two on first launch.

> **Tip:** To reopen an existing Codespace, go back to the **Codespaces** tab in the **`<> Code`** menu and select it from the list.

# Running locally
Review the local setup file: [LOCALSETUP.md](LOCALSETUP.md)     

## Python Initialization
Once the labs become available, you can use `uv` to initialize a Python virtual environment which will install
all the required dependencies for you.

Install uv
```bash
py -m pip install --upgrade uv
```

Sync the virtual environment
```bash
uv sync
```

If you open the root of the workshop folder (sansaisummit26) in VSCode, and open a terminal, it should automatically
activate the virtual environment, but if it does not, you can run the activation script so it places your console
in the scope of your venv.

```bash
.venv/Scripts/activate
```