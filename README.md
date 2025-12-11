



## 2. System Architecture and RAG Pipeline

This project has two main phases:  
1) an offline phase to build the AI knowledge base, and  
2) an online phase to answer user questions with RAG.

### 2.1 Offline phase: build the knowledge base

- Collect 18 curated AI sources (academic PDFs, policy/ethics papers, web articles, reports, YouTube transcripts) covering AI uses, benefits, risks, black-box models, and social impact.  
- Extract and clean plain text from all files.  
- Split text into small, paragraph-sized chunks.  
- Create embeddings for each chunk using `all-MiniLM-L6-v2` (384 dimensions).  
- Store chunks, embeddings, and metadata in a DuckDB vector database, wrapped by `RAGDatabase.query()`.

### 2.2 Online phase: answer questions with RAG

- The user opens the Streamlit app, enters an OpenAI API key, and asks a question in the chat.  
- The app creates a `RAGAgent` with the selected model, the `RAGDatabase` instance, and a limit on tool calls.  
- The agent uses a custom tool (`Query RAG Database`) to run a vector search in DuckDB and retrieve the top-k relevant chunks.  
- A CrewAI `Task` tells the agent to answer the question using only these passages and to follow a fixed structure (short answer, details, from the sources, next steps).  
- `RAGAgent.ask()` returns the formatted answer and the retrieved sources; Streamlit displays the answer in the chat and shows the passages in an expandable "Sources" panel.




