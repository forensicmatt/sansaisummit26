# 3-Hour Workshop Outline: Clustering and Analyzing Event Logs with LLMs

**Total Time: 180 Minutes**

# 0. Workshop Introduction (10 Minutes)
*Goal: Welcome students, provide an overview of the workshop structure, and set expectations.*
- **Welcome & Introductions:** Brief background and agenda.
- **Concepts Overview:** High-level primer on using embeddings and LLMs for cybersecurity analysis.

# 1. Download Dataset (10 Minutes)
*Goal: Ensure all students have the required dataset and environment ready.*
- **1.1 Git clone dataset:** We will pull down a repository containing sample EVTX logs (e.g., Sysmon logs) and the workshop Jupyter notebooks. We'll also verify our Python dependencies are installed.

# 2. Transform Dataset (10 Minutes)
*Goal: Parse raw EVTX files into a structured Pandas DataFrame suitable for machine learning.*
- **2.1 Custom code to transform dataset:** We'll introduce a custom `EvtxHandler` and `DocumentTransformer` to parse XML attributes into columns, specifically focusing on unique `CommandLine` arguments to reduce noise.

# 3. Generate Embeddings (20 Minutes)
*Goal: Convert string data (Command Lines) into dense vector representations.*
- **3.1 Option 1: Use OpenAI API:** We'll write an asynchronous function using `AsyncOpenAI` to request embeddings (e.g., `text-embedding-3-small`) for our unique command lines, handling rate limits with a semaphore.
- **3.2 Option 2: Use local model (Ollama):** For environments without API access or for privacy, we'll demonstrate using Ollama to generate embeddings locally on the workshop machine.

# 4. Cluster Data (30 Minutes)
*Goal: Group similar command lines together using unsupervised learning.*
- **4.1 DBSCAN Algorithm:** We'll explore DBSCAN, which is great for density-based clustering and outlier detection (finding anomalous commands).
- **4.2 KMeans Algorithm:** We'll contrast DBSCAN with KMeans, using the Silhouette Score to dynamically calculate the optimal number of clusters (k) for our dataset.

*(10 Minute Break)*

# 5. Visualizing Clusters (15 Minutes)
*Goal: Help humans understand the multi-dimensional embeddings and their groupings.*
- **5.1 PCA Algorithm:** We will use Principal Component Analysis (PCA) to reduce our high-dimensional embedding vectors down to 3 dimensions.
- **5.2 3D plotting with plotly:** We'll use `plotly.express` to create an interactive 3D scatter plot of our clustered data, allowing students to explore the command groupings visually.

# 6. LLM Risk Analysis (30 Minutes)
*Goal: Use LLMs to automatically assess the risk level of our identified clusters.*
- **6.1 Cluster Sampling:** Instead of asking the LLM to analyze every single command, we'll extract a representative sample of commands from each cluster.
- **6.2 Risk Prompting:** We'll craft a system prompt for a digital forensics analyst role and ask the LLM (e.g., `gpt-4o-mini`) to assign a risk score (0-10) to each cluster sample, outputting the result in strict JSON format.

# 7. LLM Categorization (30 Minutes)
*Goal: Further enrich our clusters by applying MITRE ATT&CK or generic functional categories.*
- **7.1 Cluster Categorization:** Expanding on the risk prompt, we will ask the LLM to provide a category or behavior description for the cluster (e.g., "Discovery", "Execution", "Benign Admin Activity").
- **7.2 Graphing Categories and Risk:** We will visualize the clusters emphasizing their risk score and category to prioritize which clusters need immediate human analyst attention.

# 8. RAG (Retrieval Augmented Generation) (15 Minutes)
*Goal: Search our dataset using natural language.*
- **8.1 Vector Database (DuckDB):** We'll ingest our command embeddings and metadata into a DuckDB database configured for vector similarity search.
- **8.2 Generate Response:** We'll query DuckDB for commands similar to a user question (e.g., "Did anyone run PowerShell encoded commands?") and use the results as context to generate a final, natural language answer.
