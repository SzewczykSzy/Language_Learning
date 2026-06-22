# Language_Learning

***PROJECT DESCRIPTION***

A mobile app for Android phones that will allow me to learn and improve my foreign language skills—English by default. I will use it every day or on other days (with the option to set custom notifications). Each learning session should include

1. Reading comprehension — The Research Agent searches for scientific articles and news, by default in the fields of AI/ML/DS and IT or on another specified topic, and then generates a text or PDF that describes exactly what is contained in that article (or several articles), so that the reading takes no more than 10 minutes. The agent retrieves the latest news using an integration with a search engine (e.g., Tavily API, Serper) or an RSS reader to avoid model hallucinations. My task will then be to verbally describe what was in the generated text or PDF. Throughout the entire cycle of the app’s operation, the same articles cannot be repeated. To prevent duplicate articles, I need a database that stores the hashes (or embeddings) of texts I've already read.

2. Conversation with a native speaker—a casual conversation, typically on topics related to AI/ML/DS and IT (I work in a corporate setting) or on another specified topic, to improve my conversational skills—this is what matters most to me. Low latency is a requirement, so streaming must be used at every stage to ensure that the conversation is not unnaturally interrupted.

3. Flashcards—Flashcards consist of a few standard words or short phrases along with their explanations. First, I type or dictate from Polish how I would say it in English. Ideally, the agent should dynamically generate flashcards based on errors from Modules 1 and 2. If I use a structure incorrectly during a conversation, the system automatically creates a flashcard from it.

4. Phrases and Vocabulary - Learning a collection of phrases commonly used in the language. The focus here is on correct sentence structure in conjunction with the phrases, in order to get closer to formulating sentences at a native speaker’s level—complete sentences, interjections, summaries, changing the subject, and the like. Finally, vocabulary worth learning, both general and specific to the topic of the day. Use the Spaced Repetition System algorithm (e.g., SuperMemo-2). Without it, vocabulary learning will not be optimized in terms of time.

5. Progress & Performance Dashboard (Standalone Module)
To keep the daily learning routine fast and frictionless, the evaluation process is not a blocking step. Instead, an "LLM-as-a-Judge" pipeline runs asynchronously on the backend immediately after a session ends. It analyzes transcripts to catch grammatical errors, evaluate conversational fluency, and extract context for dynamic flashcard generation.

The mobile app features a dedicated, separate dashboard module to present these aggregated insights. Here, I can review my performance over time, track the adoption rate of new corporate IT vocabulary or terminology related to other specified domains (e.g., automotive engineering, sports), and view historical trends. The dashboard also provides on-demand daily or weekly summaries and actionable, personalized recommendations based on my overall progress.

***END OF DESCRIPTION***

Each part of the learning process should collect data, generate assessments and results, track changes over time, and provide a summary of the assessment along with a brief description and recommendations.

Tech Stack:

The mobile app should serve solely as a user interface and audio recorder. All agent logic, the database, RAG, and integration with LM Studio must run on a laptop as a server (backend).

Core Infrastructure (Runs on a laptop)
LLM Engine: LM Studio (provides a local API compatible with the OpenAI standard).

Backend & Orchestration: FastAPI (Python). It’s fast, asynchronous (ideal for LLM streaming), and enforces good code structure.

Agent Framework: DSPs combined with LangChain (or LangGraph). DSPs are absolutely crucial here. Instead of manually “crafting” prompts for the Research Agent or the pronunciation evaluator, they can be compiled and optimized on smaller local models.

Database (Relational): SQLite or PostgreSQL. For storing session history, results, flashcard definitions, and SRS status.

Vector Database: ChromaDB or Qdrant (locally). Essential for storing read articles and the context of previous conversations (so that “Native Speaker” remembers what you talked about a week ago).

Audio Pipeline (STT/TTS):

STT: Faster-Whisper (running on the laptop’s CPU/GPU).

TTS: Coqui TTS (locally) or, to conserve resources and reduce latency, Edge TTS (a free Microsoft API that sounds very natural).

Mobile App (Client)
Frontend: React Native (Expo) or Flutter.

Evaluation Environment (TDD / LLMOps)
Experiment Tracking: MLflow. Ideal for logging DSP parameters, prompts, latency metrics, and evaluation results.

RAG and Agent Evaluation: Ragas (a RAG evaluation framework) integrated with MLflow (faithfulness and answer relevance metrics).