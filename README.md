# AI RAG Assistant: Explaining the Benefits and Risks of AI

This project is an interactive Retrieval-Augmented Generation (RAG) assistant that answers questions about artificial intelligence. It uses:

- A curated AI document collection stored in a DuckDB vector database.  
- A CrewAI agent that retrieves relevant passages and generates answers.  
- A Streamlit web app that provides a chat interface and shows answers plus sources.

The assistant relies only on these preselected AI sources and returns clear, structured answers that explicitly show how each response is grounded in the retrieved passages.

---

## 1. Domain Overview and Problem Statement

### Domain

The assistant focuses on the **benefits and risks of artificial intelligence**, especially:

- How AI is used in research, education, and everyday life.  
- How issues like bias, safety, and black-box systems affect trust and understanding.

### Problem this assistant addresses

Information about AI is often scattered, overly technical, or framed as either hype or fear. It can be hard for non-experts to get clear, reliable explanations.  
This assistant:

- Queries a curated set of AI documents instead of the open internet.  
- Generates clear, well-structured answers grounded in retrieved passages.  
- States when the sources do not provide enough information to fully answer a question.

---

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

---

## 3. Document Collection Summary

The assistant is powered by a vector database built from **18 curated sources** about artificial intelligence, including:

- Academic PDFs and policy/ethics reports  
- Web articles and plain-text reports  
- Transcripts from YouTube talks and explainers  

Across these sources, the collection covers:

- How AI is used in research, education, and everyday life  
- Benefits of AI (efficiency, new applications, decision support)  
- Risks such as bias, safety issues, and hard-to-explain "black-box" models  
- Ethical and governance questions (guidelines, codes of conduct, auditing)  
- Social and infrastructure impacts (critical services, broader society)  
- Practical guidance on using AI tools and writing effective prompts  

These documents were selected to give a **balanced view** of AI, combining technical, ethical, and practical perspectives. The goal is for the assistant’s answers to be:

- Grounded in specific, trustworthy sources  
- Clear about where information comes from  
- Honest when the documents do not provide enough detail to fully answer a question  

---

## 4. Agent Configuration Details and Why They Matter

The agent is configured so that users understand what it is good at, what it is limited to, and how to ask questions that fit it.

### 4.1 Role – "Artificial Intelligence Content Assistant"

The agent is a focused assistant for **questions about artificial intelligence**. It is designed to explain:

- How AI is used  
- What benefits it offers  
- What risks it poses  
- How it affects people and systems  

It is **not** intended for general topics, so the best results come from questions about AI concepts, uses, risks, ethics, and social or policy impact.

### 4.2 Goal – "Answer questions about artificial intelligence using the vector database."

The agent answers questions by:

- Retrieving passages from a DuckDB vector database built from the 18 curated AI sources  
- Using those passages to generate the response  

Its answers reflect this specific collection, not the entire internet. Each response is paired with a "Sources" section so users can see which passages were used.

### 4.3 Backstory – Expert using only curated sources and stating gaps

The agent is framed as an AI expert whose knowledge comes **only** from the curated documents on AI’s uses, benefits, risks, black-box behavior, and ethics. It is instructed to:

- Rely on retrieved passages, not guess or invent information  
- Clearly state when the sources do not provide enough detail to fully answer a question  

This configuration keeps the assistant:

- Focused on the AI domain  
- Grounded in the project’s document collection  
- Transparent about the limits of what the sources can support  

---

## 5. Installation and Setup

You can use this assistant in two ways: through a hosted Streamlit app or by running it locally.

### 5.1 Option 1: Use the Streamlit web app (recommended)

- Open the Streamlit app: **[Streamlit link – to be added]**.  
- In the left sidebar, paste your OpenAI API key into the "OpenAI API Key" field.  
- Keep the default settings for database path, results per query, model, and max tool calls, unless you know you want to change them.  
- Type a question about artificial intelligence in the chat box and press Enter.  
- Read the answer and, if you want to see the evidence, expand the "Sources" section under the response.

### 5.2 Option 2: Run the app locally from GitHub

- Go to the GitHub repository: **[GitHub link – to be added]** and clone it to your machine using `git clone` or the GitHub "Clone" option.  
- Open a terminal and move into the project folder (for example: `cd ai-rag-assistant`).  
- Install the required Python packages with `pip install streamlit crewai duckdb openai`.  
- Make sure the DuckDB file with the AI document collection (such as `backend/ai.duckdb`) is included in the repository or placed at the path specified in `config.py`.  
- Start the app with Streamlit (for example: `streamlit run app.py`), open the local URL shown in your terminal, enter your OpenAI API key in the sidebar, and interact with the assistant as you would in the hosted version.
