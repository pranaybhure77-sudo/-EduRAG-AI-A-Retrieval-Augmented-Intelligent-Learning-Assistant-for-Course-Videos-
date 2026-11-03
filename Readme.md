1. Project overview
🎓 RAG-Based AI Teaching Assistant

Ask questions. Retrieve lecture knowledge. Get context-based answers.







roshankodi.github.io



Purpose
The application allows students to ask questions about recorded video lectures and receive answers based on the lecture content. Instead of searching through an entire video, the system finds relevant transcript segments and passes them to an LLM to generate an answer.

Target audience
- Students revising recorded lectures.
- Teachers organizing course material.
- Online learning platforms.
- Training teams using recorded tutorials.
Use cases
- Ask questions about a particular lecture.
- Summarize a topic from a video.
- Find relevant lecture timestamps.
- Revise concepts without watching an entire recording.
- Generate study questions from lecture material.
Technology stack
Layer	Existing project	Recommended enhancement
Language	Python	Python
Transcription	Whisper large-v2	Whisper / faster-whisper
Embeddings	BGE-M3	BGE-M3
LLM	Llama 3.2 via Ollama	Ollama or hosted LLM
Retrieval	Scikit-learn cosine similarity	FAISS or Chroma
Storage	Joblib, JSON	Chroma/FAISS + metadata
Backend	Python scripts	FastAPI
Frontend	CLI	React + Tailwind CSS
Data	Video lectures	Video, PDF, text

Recommendation: Keep your existing Python pipeline as the first working version. Add FastAPI and React only after the retrieval pipeline works reliably.
2. Repository structure
Here is a suggested GitHub structure for the expanded project:
rag-ai-teaching-assistant/
│
├── frontend/
│   ├── public/
│   │   └── favicon.svg
│   └── src/
│       ├── components/
│       │   ├── ChatWindow.jsx
│       │   ├── MessageBubble.jsx
│       │   ├── SourceCard.jsx
│       │   └── Sidebar.jsx
│       ├── pages/
│       │   └── Home.jsx
│       ├── services/
│       │   └── api.js
│       └── App.jsx
│
├── backend/
│   ├── app/
│   │   ├── main.py
│   │   ├── config.py
│   │   ├── api/
│   │   │   └── routes.py
│   │   ├── rag/
│   │   │   ├── embeddings.py
│   │   │   ├── retriever.py
│   │   │   └── generator.py
│   │   └── ingestion/
│   │       ├── video_loader.py
│   │       ├── transcriber.py
│   │       └── chunker.py
│   └── requirements.txt
│
├── data/
│   ├── videos/
│   ├── audios/
│   └── jsons/
│
├── models/
│   └── README.md
│
├── scripts/
│   ├── video_to_mp3.py
│   ├── mp3_to_json.py
│   ├── preprocess_json.py
│   └── process_incoming.py
│
├── tests/
│   ├── test_chunking.py
│   └── test_retrieval.py
│
├── docs/
│   ├── architecture.md
│   └── api.md
│
├── .github/
│   └── workflows/
│       └── python-tests.yml
│
├── .env.example
├── .gitignore
├── CONTRIBUTING.md
├── CODE_OF_CONDUCT.md
├── LICENSE
├── README.md
└── docker-compose.yml


This is the proposed expanded structure. Your current scripts can initially remain under scripts/; move them into backend/app/ as you modularize the project.
3. End-to-end RAG workflow
How the system works
1. Video lectures
Input learning material

2. FFmpeg + Whisper
Extract audio and transcribe lectures

3. Transcript chunks
Store text with lecture titles and timestamps

4. BGE-M3 embeddings
Convert text into numerical vectors

5. Vector storage
Current: Joblib · Future: FAISS or Chroma

6. User question
Convert question into an embedding

7. Semantic retrieval
Find the five most relevant lecture chunks

8. Llama 3.2
Generate an answer using retrieved context

9. Final response
Answer with lecture title and timestamps

Explanation of each stage
1. Ingestion: Read the video files from the videos/ folder.
2. Audio extraction: FFmpeg converts videos into MP3 files.
3. Transcription: Whisper converts audio into text and segment timestamps.
4. Preprocessing: Save transcript metadata in JSON format.
5. Embedding: BGE-M3 converts each transcript chunk into a numerical vector.
6. Indexing: Save vectors and metadata in Joblib. In an enhanced version, use FAISS or Chroma.
7. Retrieval: Embed the user's question and calculate cosine similarity.
8. Generation: Send the question and top five retrieved chunks to Llama 3.2.
9. Response: Display the generated answer and relevant lecture references.
4. Implementation plan: MVP to advanced version
Build the application in stages so that you always have a working project.
Phase 1 — MVP: working RAG application
Start here
Feature	Implementation
Video input	Read files from videos/
Audio extraction	FFmpeg
Transcription	Whisper large-v2
Embeddings	BGE-M3 through Ollama
Retrieval	Cosine similarity
Answer generation	Llama 3.2
Interface	Existing command-line interface

MVP success criteria: A user can ask a question and receive an answer grounded in the lecture transcripts, with the lecture title and timestamps available as references.
Phase 2 — Web application
- Create a FastAPI backend.
- Build a React chat interface.
- Display retrieved sources below each answer.
- Add a loading indicator while the model generates a response.
- Add a lecture upload page.
- Add a clear-chat button and conversation history.
Phase 3 — Advanced features
- User authentication and individual accounts.
- Multi-course and multi-user support.
- FAISS or Chroma vector database.
- PDF and DOCX ingestion.
- Adjustable number of retrieved chunks.
- Answer feedback and evaluation.
- Accessibility, dark/light themes and mobile responsiveness.
- Automated tests and deployment.
5. UI/UX design
The web interface should look like a clean AI learning assistant rather than a generic chatbot.




















15













Suggested interface layout
Area	Components
Sidebar	Logo, new chat, courses, chat history
Header	Project name, model status, theme toggle
Chat area	User questions and AI answers
Source panel	Lecture title, timestamp, relevant text
Input area	Text input, send button, attachment button
Footer	Model name and privacy information

Interactive elements and icons
Use Lucide icons through lucide-react.
Feature	Icon
New conversation	MessageSquarePlus
Search lectures	Search
Upload file	Upload
Send question	Send
AI answer	Sparkles
Source references	BookOpen
Lecture history	History
Settings	Settings
Copy answer	Copy
Theme switch	Sun / Moon
Model status	Activity
Clear chat	Trash2

Interaction guidelines
- Hover: show a subtle background and tooltip on icon buttons.
- Focus: show a visible keyboard focus outline.
- Loading: display a spinner or animated three-dot indicator.
- Success: show a clear upload or indexing confirmation.
- Error: show a readable error message with a retry action.
- Mobile: collapse the sidebar into a menu.
- Accessibility: use labels, semantic HTML, keyboard navigation, sufficient contrast and screen-reader-friendly status messages.
6. Example code snippets
The following examples are for the proposed upgraded architecture. They are not claims that these files already exist in your current repository.
A. Generate embeddings using Ollama
Install the required packages:
pip install requests numpy pandas scikit-learn chromadb fastapi uvicorn


Create backend/app/rag/embeddings.py:
import requestsOLLAMA_URL = "http://localhost:11434/api/embed"EMBED_MODEL = "bge-m3"def create_embedding(text: str):    response = requests.post(        OLLAMA_URL,        json={            "model": EMBED_MODEL,            "input": text        },        timeout=120    )    response.raise_for_status()    return response.json()["embeddings"][0]



B. Store transcript chunks in Chroma
Create backend/app/rag/vector_store.py:
import chromadbfrom backend.app.rag.embeddings import create_embeddingclient = chromadb.PersistentClient(path="./chroma_db")collection = client.get_or_create_collection(    name="lecture_chunks")def add_chunk(chunk_id, text, metadata):    vector = create_embedding(text)    collection.upsert(        ids=[str(chunk_id)],        documents=[text],        embeddings=[vector],        metadatas=[metadata]    )def search_chunks(question, top_k=5):    query_vector = create_embedding(question)    return collection.query(        query_embeddings=[query_vector],        n_results=top_k    )



Example metadata:
metadata = {    "title": "Introduction to Python",    "number": 1,    "start": 120.5,    "end": 145.2}



C. Create a RAG generation function
Create backend/app/rag/generator.py:
import requestsOLLAMA_URL = "http://localhost:11434/api/generate"def generate_answer(question, context):    prompt = f"""    You are an AI teaching assistant.    Answer the question using only the    lecture context provided below.    If the answer is not in the context,    clearly say that you could not find it.    Context:    {context}    Question:    {question}    Answer:    """    response = requests.post(        OLLAMA_URL,        json={            "model": "llama3.2",            "prompt": prompt,            "stream": False        },        timeout=180    )    response.raise_for_status()



D. Build a FastAPI endpoint
Create backend/app/main.py:
from fastapi import FastAPIfrom pydantic import BaseModelfrom backend.app.rag.vector_store import search_chunksfrom backend.app.rag.generator import generate_answerapp = FastAPI(title="RAG Teaching Assistant")class Question(BaseModel):    question: str@app.post("/ask")def ask_question(data: Question):    results = search_chunks(data.question, top_k=5)    documents = results["documents"][0]    metadatas = results["metadatas"][0]    context_parts = []    for text, metadata in zip(documents, metadatas):        context_parts.append(            f"Lecture: {metadata['title']}\n"            f"Timestamp: {metadata['start']} - "            f"{metadata['end']}\n"            f"Content: {text}"        )    context = "\n\n".join(context_parts)    answer = generate_answer(        data.question,        context    )    return {        "question": data.question,        "answer": answer,        "sources": metadatas    }



Run the backend:
uvicorn backend.app.main:app --reload


API documentation will be available at:

http://127.0.0.1:8000/docs
E. React frontend: send a question to the backend
Create frontend/src/services/api.js:
const API_URL = "http://127.0.0.1:8000";

export async function askQuestion(question) {
  const response = await fetch(`${API_URL}/ask`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify({ question })
  });

  if (!response.ok) {
    throw new Error("Unable to get an answer");
  }

  return response.json();
}


Example React component:
import { useState } from "react";
import { askQuestion } from "./services/api";

export default function Chat() {
  const [question, setQuestion] = useState("");
  const [answer, setAnswer] = useState("");
  const [loading, setLoading] = useState(false);

  async function handleAsk() {
    if (!question.trim() || loading) return;

    setLoading(true);
    setAnswer("");

    try {
      const result = await askQuestion(question);
      setAnswer(result.answer);
    } catch (error) {
      setAnswer("Something went wrong. Please try again.");
    } finally {
      setLoading(false);
    }
  }

  return (
    <main>
      <h1>🎓 Study Assistant</h1>

      <input
        value={question}
        onChange={(e) => setQuestion(e.target.value)}
        placeholder="Ask something about your lecture..."
      />

      <button onClick={handleAsk} disabled={loading}>
        {loading ? "Thinking..." : "✨ Ask AI"}
      </button>

      <section aria-live="polite">
        {answer && <p>{answer}</p>}
      </section>
    </main>
  );
}


Important implementation notes
- Enable CORS on FastAPI for your frontend's development origin.
- Add error handling, input limits and request validation before public deployment.
- The sample API currently returns source metadata but the React example only displays the answer. Extend the component to render the returned sources.
- Chroma's upsert approach is an enhancement; your existing implementation uses Joblib and Scikit-learn.
- Keep the embedding model the same when indexing documents and embedding questions. Changing models requires rebuilding the index.
- The snippets are a starting point, not a complete production-ready security implementation.
7. GitHub-ready deliverables
A. .gitignore
# Python
__pycache__/
*.py[cod]
.venv/
venv/
.env

# Data and model files
data/videos/*
data/audios/*
data/jsons/*
*.joblib
*.pt
*.bin
chroma_db/

# Keep folder placeholders
!data/videos/.gitkeep
!data/audios/.gitkeep
!data/jsons/.gitkeep

# Frontend
node_modules/
dist/
build/

# IDE
.vscode/
.idea/

# OS
.DS_Store
Thumbs.db

# Test and cache
.pytest_cache/
.coverage


Do not upload private lecture videos, API keys, personal information or large model weights to GitHub.
B. MIT License
Create LICENSE and use the standard MIT License text, replacing the copyright holder and year with your own details. The MIT License permits use, modification and redistribution subject to its conditions.
C. CONTRIBUTING.md
# Contributing

Thank you for your interest in contributing!

## How to contribute

1. Fork this repository.
2. Create a new branch.
3. Make your changes.
4. Run the tests.
5. Submit a pull request.

## Guidelines

- Write readable and maintainable code.
- Follow existing project structure.
- Add tests for new features.
- Update documentation.
- Never commit API keys or private data.


D. CODE_OF_CONDUCT.md
Use the standard Contributor Covenant and include a contact address that you control.
E. GitHub Actions CI
Create .github/workflows/python-tests.yml:
name: Python Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install pytest ruff
          pip install -r backend/requirements.txt

      - name: Lint
        run: ruff check backend tests

      - name: Test
        run: pytest -q


Add actual tests before relying on this workflow. For example, test transcript chunking, embedding response validation and retrieval metadata. CI should mock Ollama calls rather than depend on a running local model.
F. API documentation
Document at least these endpoints as the application grows:
Method	Endpoint	Purpose
GET	/health	Check backend status
POST	/ask	Ask a question
POST	/ingest	Add lecture content
GET	/lectures	List available lectures
DELETE	/lectures/{id}	Delete a lecture

For each endpoint, document request fields, response fields, errors and authentication requirements.
8. Security and ethical considerations
A RAG application can expose private learning material if it is not designed carefully.
Area	Recommended practice
Privacy	Do not upload private lecture recordings without permission.
API keys	Store secrets in environment variables.
Authentication	Require login before accessing private courses.
File uploads	Validate file type, size and content.
Data access	Ensure users can retrieve only authorized course content.
Prompt injection	Treat retrieved documents as untrusted input.
Model output	Tell users that AI-generated answers may contain errors.
Copyright	Respect the rights of lecture owners and content creators.
Data deletion	Provide a way to delete uploaded lectures and their embeddings.
Transparency	Explain whether processing happens locally or through external services.

Privacy advantage of your existing approach: Ollama can run models locally, and your existing pipeline can process material on the local machine. However, local processing does not automatically guarantee privacy; uploaded files, logs, backups and access controls must also be handled properly.
9. README.md — ready-to-adapt GitHub content
Below is the recommended README content for your repository. It describes your existing CLI project accurately while identifying the web application as a future enhancement.
🎓 RAG-Based AI Teaching Assistant
<p align="center">
  <strong>Ask questions about video lectures and get context-aware AI answers.</strong>
</p>

<p align="center">
  ![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
  ![RAG](https://img.shields.io/badge/Architecture-RAG-purple)
  ![Whisper](https://img.shields.io/badge/Transcription-Whisper-green)
  ![Ollama](https://img.shields.io/badge/LLM-Ollama-black)
  ![License](https://img.shields.io/badge/License-MIT-yellow)
</p>
📌 Table of Contents
- Overview
- Features
- Technology Stack
- Architecture
- Repository Structure
- Installation
- Usage
- How RAG Works
- Future Enhancements
- Contributing
- License
🌟 Overview
The RAG-Based AI Teaching Assistant is a Python application that helps students understand recorded video lectures through natural-language questions.

It uses Retrieval-Augmented Generation (RAG) to retrieve relevant lecture transcript segments and provide context-based answers using a locally running Large Language Model.

Instead of manually searching through long videos, students can ask questions and receive answers based on the available lecture content.
✨ Features
- 🎥 Extract audio from video lectures using FFmpeg.
- 🎙️ Convert speech into text using OpenAI Whisper.
- 🧠 Generate text embeddings using BGE-M3.
- 🔎 Retrieve relevant content using cosine similarity.
- 🤖 Generate answers using Llama 3.2.
- ⏱️ Preserve lecture titles and segment timestamps.
- 💻 Run language models locally using Ollama.
🛠️ Technology Stack
Technology	Purpose
Python	Main programming language
FFmpeg	Audio extraction
Whisper large-v2	Speech transcription
BGE-M3	Text embeddings
Ollama	Local model execution
Llama 3.2	Answer generation
NumPy	Numerical operations
Pandas	Transcript and embedding data
Scikit-learn	Cosine similarity
Joblib	Save and load embeddings🏗️ Architecture
flowchart TD
    A[Video Lectures] --> B[FFmpeg]
    B --> C[MP3 Audio]
    C --> D[Whisper]
    D --> E[Transcript JSON]
    E --> F[BGE-M3 Embeddings]
    F --> G[Joblib Storage]
    H[User Question] --> I[Question Embedding]
    I --> J[Cosine Similarity]
    G --> J
    J --> K[Top 5 Relevant Chunks]
    K --> L[Llama 3.2]
    L --> M[AI Answer]
📁 Repository Structure
rag-ai-teaching-assistant/
├── videos/
├── audios/
├── jsons/
├── video_to_mp3.py
├── mp3_to_json.py
├── preprocess_json.py
├── process_incoming.py
├── embeddings.joblib
├── prompt.txt
├── response.txt
└── README.md
⚙️ Installation
1. Clone the repository
git clone YOUR_GITHUB_REPOSITORY_URL
cd rag-ai-teaching-assistant
2. Create a virtual environment
python -m venv .venv
Activate it on Windows:
.venv\Scripts\activate
3. Install Python dependencies
python -m pip install --upgrade pip
pip install openai-whisper requests numpy pandas scikit-learn joblib
Whisper also requires a compatible PyTorch installation. Install PyTorch according to your operating system and hardware.
4. Install FFmpeg
Install FFmpeg and ensure the ffmpeg command is available in your system PATH.
5. Install Ollama
Download Ollama from:

https://ollama.com/

Pull the required models:
ollama pull bge-m3
ollama pull llama3.2
Make sure the Ollama service is running locally.
🚀 Usage
Place your lecture videos in the videos/ folder.

Run the scripts in the following order:

Step 1 — Extract audio
python video_to_mp3.py
Step 2 — Transcribe audio
python mp3_to_json.py
Step 3 — Generate embeddings
python preprocess_json.py
Step 4 — Ask questions
python process_incoming.py
The application retrieves relevant transcript segments and generates an answer using Llama 3.2.
🔍 How RAG Works
1. Retrieval: The user's question is converted into an embedding.
2. Similarity Search: Cosine similarity identifies the most relevant transcript chunks.
3. Context Creation: The retrieved chunks are combined with lecture metadata.
4. Generation: Llama 3.2 generates an answer based on the retrieved context.
🖥️ Requirements
- Python and compatible PyTorch installation.
- FFmpeg.
- Ollama.
- BGE-M3 model.
- Llama 3.2 model.
- Sufficient RAM and disk space for model execution and lecture processing.
🔮 Future Enhancements
- 🌐 React-based web interface.
- 💬 Interactive chatbot.
- 📚 PDF and document ingestion.
- 🗃️ FAISS or Chroma vector database.
- 👤 User authentication.
- 📱 Responsive mobile interface.
- 📊 Answer evaluation and feedback.
- 🔗 Clickable lecture timestamp references.
🤝 Contributing
Contributions are welcome!

Please read CONTRIBUTING.md before submitting changes.
🔐 Privacy
Use lecture materials only when you have permission to process them. Do not commit private recordings, credentials or sensitive information.
📄 License
This project is distributed under the MIT License. See LICENSE for details.

<p align="center">
  ⭐ If you find this project useful, consider giving it a star!
</p>




Before publishing: replace YOUR_GITHUB_REPOSITORY_URL, verify the dependency versions, and ensure your actual repository folders match the README. The sample README intentionally does not claim that the proposed React interface or Chroma database is already implemented.
10. Run instructions and optional Docker setup
Run your existing project locally
First, make sure these folders exist:
videos/
audios/
jsons/


Then:
# Activate environment
.venv\Scripts\activate

# Start Ollama if it is not already running
ollama serve

# Download models (one-time setup)
ollama pull bge-m3
ollama pull llama3.2

# Run the RAG pipeline
python video_to_mp3.py
python mp3_to_json.py
python preprocess_json.py
python process_incoming.py


Your current pipeline uses these model settings:
- Whisper: large-v2
- Transcription language: Hindi (hi)
- Whisper task: translate
- Embedding model: bge-m3
- Generation model: llama3.2
- Retrieval: top 5 chunks
Optional Docker Compose for the upgraded application
Once you have created the FastAPI backend and a Dockerfile, you can use Docker Compose to run the API alongside Ollama.
