# Language_Learning

***PROJECT DESCRIPTION***

A mobile app for Android phones that will allow me to learn and improve my foreign language skills—English by default. I will use it every day or on other days (with the option to set custom notifications). Each learning session should include steps:

1. Reading comprehension — The Research Agent searches for scientific articles and news, by default in the fields of AI/ML/DS and IT or on another specified topic (It is choosen by me at the beginning of the session). Then, it generates a text or PDF that describes exactly what is contained in that article (or several articles), so that the reading takes no more than 10 minutes. The agent retrieves the latest news using a locally hosted metasearch engine (SearXNG in a Docker container) to avoid model hallucinations and external API limits. Task of the user will then be to verbally describe what was in the generated text or PDF. 
If the user does not describe the text in a configurable amount of words, the LLM asks follow-up questions about skipped topics. If the user exceeds the threshold, the agent analyzes the text and uses any bad wording or simple phrasing as context for the next steps. Throughout the entire cycle of the app’s operation, the same articles cannot be repeated. To prevent duplicate articles, there will be a database that stores the hashes (or embeddings) of texts user already read.
When the module is finished, a popup button will allow the user to proceed to the next section.

2. Conversation with a native speaker — A casual conversation, typically on topics related to AI/ML/DS and IT or on another specified topic (remembered from previous step), to improve my conversational skills — this is what matters most to the user the most. Low latency is a requirement, so streaming must be used at every stage to ensure that the conversation is not unnaturally interrupted. Error correction does not happen during the conversation; mistakes are logged and converted to flashcards later.
When the module is finished, a popup button will allow the user to proceed to the next section.

3. Flashcards — Flashcards use a Contextual/Concept-based format (Cloze deletion). If the user made a mistake in previous modules, the flashcard will show their original incorrect sentence, followed by a rephrased sentence with a blank. When flipped, it shows the proper word/sentence. If there are no mistakes, the agent should suggest some flashcards with interesting words or phrases.

4. Phrases and Vocabulary - Learning a collection of phrases commonly used in the language. The focus here is on correct sentence structure in conjunction with the phrases, in order to get closer to formulating sentences at a native speaker’s level—complete sentences, interjections, summaries, changing the subject, and the like. Finally, vocabulary worth learning, both general and specific to the topic of the day. Use the Spaced Repetition System algorithm (e.g., SuperMemo-2). Without it, vocabulary learning will not be optimized in terms of time.

5. Progress & Performance Dashboard (Standalone Additional Module)
To keep the daily learning routine fast and frictionless, the evaluation process is not a blocking step. Instead, an "LLM-as-a-Judge" pipeline (workflow) runs asynchronously on the backend immediately after a session ends. It analyzes transcripts to catch grammatical errors, evaluate conversational fluency.

The mobile app features a dedicated, separate dashboard module to present these aggregated insights. Here, user can review performance over time, track the adoption rate of new corporate IT vocabulary or terminology related to other specified domains (e.g., automotive engineering, sports), and view historical trends. The dashboard also provides on-demand daily or weekly summaries and actionable, personalized recommendations based on the user's overall progress.

***END OF DESCRIPTION***

Each part of the learning process should collect data, generate assessments and results, track changes over time, and provide a summary of the assessment along with a brief description and recommendations.

Tech Stack:

The mobile app should serve solely as a user interface and audio recorder. All agent logic, the database, RAG, evaluation logic, and integration with LM Studio must run on a laptop as a server (backend). Mobile phone connects with backend via WiFi using standard WebSockets for real-time audio streaming and data exchange.

Core Infrastructure (Runs on a laptop)
LLM Engine: LM Studio (provides a local API compatible with the OpenAI standard). It will run on Windows and be accessed from the WSL backend via the 'Serve on Local Network' option. Every service runs locally or uses free-tier APIs. As a result, in developement stage usage of `agents-cli eval`/`agents-cli dataset` or others avaiable in ADK for evaluation will not be used.

Backend & Orchestration: FastAPI (Python). It’s fast, asynchronous (ideal for LLM streaming and WebSockets), and enforces good code structure.

Agent Framework: ADK (probably using with DSPy).

Database (Relational & Vector): SQLite with the `sqlite-vec` extension. This handles both standard relational data (history, artifacts, flashcards) and vector embeddings (articles, context) in a single, lightweight local database.

Audio Pipeline (STT/TTS):

STT: Faster-Whisper (running on the laptop’s CPU/GPU), processing audio in chunks initially. A VAD (Voice Activity Detection) layer (e.g., Silero VAD) will be implemented in the future for true dynamic streaming.

TTS: Kokoro TTS (running locally). Chunked TTS will be implemented to stream audio back as the LLM generates tokens for a low-latency experience without relying on cloud APIs.

Mobile App (Client)
Frontend: React Native (Expo).

Evaluation Environment (TDD / LLMOps)
Experiment Tracking: MLflow (hosted locally via Docker). Ideal for logging DSP parameters, prompts, latency metrics, and evaluation results.

RAG and Agent Evaluation: Ragas (a RAG evaluation framework) integrated with MLflow (faithfulness and answer relevance metrics).