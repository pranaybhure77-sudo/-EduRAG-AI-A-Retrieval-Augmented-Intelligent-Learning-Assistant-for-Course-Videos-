1. Project overview
🎓 RAG-Based AI Teaching Assistant

Ask questions. Retrieve lecture knowledge. Get context-based answers.
![image alt](https://github.com/pranaybhure77-sudo/-EduRAG-AI-A-Retrieval-Augmented-Intelligent-Learning-Assistant-for-Course-Videos-/blob/43432035696bdf488e21b66728e8be870bfcbd22/Screenshot%202026-11-03%20140339.png)
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

# 🎓 RAG-Based AI Teaching Assistant

An AI-powered teaching assistant that transforms educational video lectures into searchable knowledge. Ask questions about your lectures and receive context-aware answers with relevant video titles and timestamps.

## 🚀 Overview

The RAG-Based AI Teaching Assistant uses Retrieval-Augmented Generation (RAG) to help students understand and revise educational content efficiently.

It extracts audio from video lectures using FFmpeg, converts speech into timestamped transcripts using OpenAI Whisper Large-v2, generates semantic embeddings with BGE-M3, and retrieves relevant content using cosine similarity.

Finally, Llama 3.2 running through Ollama generates answers based on the retrieved lecture context.

## ✨ Key Features

- 🎥 Video-to-Audio Conversion using FFmpeg.
- 🎙️ Automatic Speech Recognition using Whisper Large-v2.
- ⏱️ Timestamp-Based Transcription.
- 🧠 Semantic Embeddings using BGE-M3.
- 🔍 Contextual Retrieval using Cosine Similarity.
- 📚 Top-5 Relevant Chunk Retrieval.
- 🤖 AI Answer Generation using Llama 3.2.
- 🔗 Video References and Timestamp-Based Sources.
- 🖥️ Local Model Execution through Ollama.

## 🛠️ Technology Stack

| Technology | Purpose |
|---|---|
| Python | Main programming language |
| FFmpeg | Audio extraction |
| Whisper Large-v2 | Speech recognition |
| BGE-M3 | Text embeddings |
| Ollama | Local AI model execution |
| Llama 3.2 | Answer generation |
| Scikit-learn | Cosine similarity |
| NumPy | Numerical operations |
| Pandas | Data processing |
| Joblib | Embedding storage |
| JSON | Transcript storage |

## ⚙️ How It Works

1. Upload or provide an educational video.
2. FFmpeg extracts audio from the video.
3. Whisper converts audio into timestamped text.
4. Transcript text is divided into smaller chunks.
5. BGE-M3 generates semantic embeddings.
6. Embeddings and metadata are stored using Joblib.
7. User questions are converted into embeddings.
8. Cosine similarity retrieves the five most relevant chunks.
9. Llama 3.2 generates an answer using retrieved context.
10. The system provides an answer with relevant video references and timestamps.

## 📂 Project Structure

```text
rag-based-ai-teaching-assistant/
├── videos/
├── audios/
├── transcripts/
├── embeddings/
├── src/
├── data/
├── docs/
├── tests/
├── README.md
├── requirements.txt
├── .gitignore
└── LICENSE
```

## 💻 Installation

### 1. Clone the repository

```bash
git clone YOUR_GITHUB_REPOSITORY_URL
cd rag-based-ai-teaching-assistant
```

### 2. Create a virtual environment

```bash
python -m venv venv
```

Activate it:

Windows:

```bash
venv\Scripts\activate
```

Linux/macOS:

```bash
source venv/bin/activate
```

### 3. Install dependencies

```bash
python -m pip install -r requirements.txt
```

Install FFmpeg separately and ensure it is available in your system PATH.

### 4. Install and configure Ollama

Download Ollama from:

https://ollama.com/

Pull the required model:

```bash
ollama pull llama3.2
```

Ensure your BGE-M3 embedding model is also available through the configured Ollama setup.

### 5. Run the project

Run your video processing, transcription, embedding and query scripts in the order defined by your implementation.

Example:

```bash
python video_to_mp3.py
python audio_to_text.py
python generate_embeddings.py
python rag_pipeline.py
```

Replace these filenames with the actual script names in your repository.

## 💬 Example Interaction

**User Question:**

What is Python and why is it used?

**AI Assistant:**

Python is a high-level programming language known for its simple syntax and readability. It is widely used in web development, automation, data science and artificial intelligence.

**Source:** Introduction to Python Lecture

**Timestamp:** 00:10 – 00:25

## 🔮 Future Enhancements

- React-based web interface.
- FastAPI backend.
- ChromaDB or FAISS vector database.
- User authentication and multi-user support.
- PDF and document ingestion.
- Voice-based question answering.
- Conversation history.
- Interactive lecture playback with timestamps.

## 🔒 Privacy and Security

- Keep personal lecture files private.
- Do not upload API keys or credentials to GitHub.
- Run local models through Ollama where appropriate.
- Validate input files before processing.
- Restrict access to private educational content.
- Clearly disclose any external services used.

## 🎯 Project Goal

To make educational video lectures more accessible, searchable and interactive through Retrieval-Augmented Generation.

## 👨‍💻 Author

Developed as an AI and Machine Learning project.

## 📄 License

This project is licensed under the MIT License.
