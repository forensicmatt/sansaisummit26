# Workshop Cheatsheet — SANS AI Summit 2026

---

## Notebook Flow & Files

| # | Notebook | Reads | Writes |
|---|---|---|---|
| 1 | Download Dataset | — | — |
| 2 | Transform Dataset | `datasets/EVTX-ATTACK-SAMPLES/` | `_02_evtx_commands.parquet` |
| 3 | Generate Embeddings | `_02_evtx_commands.parquet` | `_03_evtx_commands_with_embeddings.parquet` |
| 4 | Cluster Data | `_03_evtx_commands_with_embeddings.parquet` | `_04_clustered_commands.parquet` |
| 5 | Visualizing Clusters | `_04_clustered_commands.parquet` | `plot.html` |
| 6 | LLM Risk Analysis | `_04_clustered_commands.parquet` | `_06_unique_clustered_commands_with_risk.parquet/.csv` |
| 7 | Similarity Sorting | `_04_clustered_commands.parquet` | — |
| 8 | RAG | `_06_unique_clustered_commands_with_risk.parquet` | — |

---

## Key Classes & Functions

### `etl.py` — EVTX Parsing Helpers
| Name | What it does |
|---|---|
| `DocumentTransformer.from_fields([(col, jmespath), ...])` | Defines which fields to extract from an EVTX record using JMESPath paths |
| `DocumentTransformer.from_fields` — fallback syntax | Use `\|\|` in the path string to try a second path if the first is null |
| `Filter.from_pattern(jmespath_expr)` | Creates a row filter; only records where the expression is truthy are kept |
| `EvtxHandler.from_source(path)` | Opens an EVTX file or folder |
| `handler.with_transformer(dt)` | Attaches a `DocumentTransformer` to the handler |
| `handler.with_filter(f)` | Attaches a `Filter` to the handler |
| `handler.iterate_records()` | Yields raw parsed dicts (useful for exploring structure) |
| `handler.parse_into_dataframe()` | Applies transformer + filter and returns a `pd.DataFrame` |

### Embeddings (LangChain)
| Class | Provider | Key args |
|---|---|---|
| `OpenAIEmbeddings` | OpenAI / Azure | `model="text-embedding-3-large"`, `api_key=SecretStr(...)` |
| `GoogleGenerativeAIEmbeddings` | Google AI Studio | `model="gemini-embedding-001"`, `task_type="RETRIEVAL_DOCUMENT"` |
| `OllamaEmbeddings` | Local Ollama | `model="embeddinggemma"` |
| `InMemoryRateLimiter` | (any) | `requests_per_second`, `max_bucket_size` — attach via `rate_limiter=` |

Core methods: `embed_documents(list[str])` → list of vectors · `embed_query(str)` → single vector

### Chat Models (LangChain)
| Class | Provider | Key args |
|---|---|---|
| `ChatOpenAI` | OpenAI / Azure | `model="gpt-4o-mini"`, `api_key=SecretStr(...)` |
| `GoogleGenerativeAI` | Google AI Studio | `model="Gemini 2.5 Pro"`, `api_key=SecretStr(...)` |
| `ChatOllama` | Local Ollama | `model="gemma2"` |

`chat_model.with_structured_output(PydanticModel, method="json_schema")` → forces typed JSON response

### Clustering (scikit-learn)
| Class / Function | Key args | Notes |
|---|---|---|
| `DBSCAN` | `eps`, `min_samples`, `n_jobs=-1` | Label `-1` = noise/unclustered |
| `KMeans` | `n_clusters=k` | Every point gets a label; no noise |
| `silhouette_score(X, labels)` | — | Range –1→+1; higher = better separation; used to pick best `k` |
| `PCA(n_components)` | `n_oversamples=1` | Reduces embedding dimensions for plotting; call `.fit()` then `.transform()` |
| `cosine_similarity(query_vec, matrix)` | — | Returns similarity scores; sort descending for closest matches |

### LangChain — RAG Components
| Class / Function | What it does |
|---|---|
| `Document(page_content, metadata={})` | Wraps a string + arbitrary dict for the vector store |
| `InMemoryVectorStore(embedding=client)` | In-memory vector store; no external DB needed |
| `vector_store.as_retriever(search_kwargs={"k": N})` | Returns top-N most similar documents to a query |
| `ChatPromptTemplate.from_messages([...])` | Builds a system + human prompt template |
| `StrOutputParser()` | Extracts the plain string from an LLM response |
| `RunnablePassthrough()` | Passes the input value through unchanged in an LCEL chain |

LCEL chain syntax: `chain = step1 | step2 | step3` → `chain.invoke(input)`

### Pydantic — Structured Output Schema
```python
class RiskAnalysis(BaseModel):
    cluster_description: str = Field(description="...")
    risk_score: int          = Field(description="0–10")
```
Pass the class to `chat_model.with_structured_output(RiskAnalysis)`.

---

## Key Concepts

| Concept | One-liner |
|---|---|
| **EVTX** | Windows binary event log format; parsed to nested dicts by the `evtx` library |
| **JMESPath** | Query language for nested JSON/dict structures — used to pluck fields from EVTX records |
| **Embeddings** | Fixed-length float vectors capturing semantic meaning; similar text → nearby vectors |
| **DBSCAN** | Density-based clustering; no `k` required; noisy/outlier points get label `-1` |
| **KMeans** | Centroid-based clustering; requires `k` upfront; every point is assigned a cluster |
| **Silhouette score** | Measures cluster quality (–1 to +1); used here to automatically select the best `k` |
| **PCA** | Reduces high-dimensional vectors to 3-D so they can be plotted |
| **Cosine similarity** | Angle-based similarity; 1 = same direction, 0 = orthogonal |
| **Structured output** | LLM constrained to return a Pydantic model — no free-text parsing needed |
| **LCEL** | LangChain Expression Language; compose pipeline steps with the `\|` operator |
| **RAG** | Retrieve semantically relevant docs from a vector store, then generate an answer with an LLM |

---

## Gotchas

- **Run notebooks in order** — each reads the Parquet produced by the one before it.
- **Use the same embedding model throughout** — mixing providers corrupts similarity scores.
- **DBSCAN `eps` is sensitive** — small changes dramatically shift the number of clusters.
- **`async`/`await` works at top level in Jupyter** — no need for `asyncio.run()`.
- **Wrap API keys in `SecretStr`** — prevents accidental logging/exposure.
- **Call `np.stack(df["Embedding"])` before sklearn** — converts a column of lists to a 2-D matrix.
