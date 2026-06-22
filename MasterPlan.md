# 1. Project Overview & Business Context
The objective is to build a mobile Android app to facilitate daily foreign language learning and improvement (primarily English). The application targets a frictionless, daily routine encompassing reading comprehension, conversational practice, vocabulary acquisition, and performance tracking. The core value lies in leveraging local, private LLM infrastructure to create an immersive, personalized, and low-latency learning experience tailored to the user's professional interests (e.g., AI/ML/DS, IT).

# 2. Technology Stack & Core Invariants
- **Client**: React Native (Expo) or Flutter.
- **Backend & Orchestration**: FastAPI (Python) - asynchronous for streaming.
- **Agent Framework**: DSPy combined with LangChain or LangGraph.
- **LLM Engine**: LM Studio (Local API, OpenAI compatible).
- **Audio Pipeline**: Faster-Whisper (STT, local CPU/GPU) and Coqui TTS (local) or Edge TTS (Microsoft API).
- **Databases**: SQLite or PostgreSQL (Relational); ChromaDB or Qdrant (Vector).
- **Evaluation/MLOps**: MLflow (experiment tracking), Ragas (RAG evaluation).
- **Core Invariants**: All backend logic, databases, and heavy processing must run locally on a laptop acting as a server. The mobile app is strictly a UI and audio recorder.
- **Hardware Constraints**: The host laptop features an AMD Ryzen 7 6800HS, 40GB RAM, and an AMD Radeon RX 6700S with 8GB VRAM. System architecture and model selection (LLM, Whisper) must strictly operate within this 8GB VRAM envelope (or utilize CPU offloading to the 40GB system RAM).

# 3. Target Architecture
A Client-Server architecture where the Android mobile app communicates with a local laptop server strictly over the local Wi-Fi network (no public internet exposure). The mobile app must support dynamic IP discovery or manual IP entry to connect to the laptop as it moves between different Wi-Fi networks. The mobile app handles the user interface and captures/streams audio. The FastAPI backend orchestrates all business logic: integrating with local LLMs via LM Studio, performing speech-to-text and text-to-speech, managing both relational and vector databases, and running evaluation pipelines.

# 4. Non-Functional Requirements
- **Local Network Only**: Strict adherence to local Wi-Fi communication for security and simplicity.
- **Streaming & UX Loading States**: Data must be streamed at every stage of the conversational pipeline. When blocking tasks occur (like flashcard generation), the UI must provide a continuous streaming loading screen rather than a silent block.
- **Uniqueness**: The system must prevent duplicate articles using hashes/embeddings to ensure fresh content.
- **Hardware Optimization**: Instead of a strict latency threshold, the priority is optimizing the overall subjective user experience while balancing model capabilities against the 8GB VRAM constraint.

# 5. Functional Requirements (End-State)
- **Module 1 (Reading Comprehension)**: User selects a topic field (default AI/ML/LLM/DS). The Research Agent scrapes recent articles from a hardcoded config list of sources (e.g., Wikipedia, arXiv). If scraping fails, the user proposes a different topic. The agent generates a 10-minute text/PDF summary -> user verbally describes content -> system verifies uniqueness via DB embeddings.
- **Module 2 (Native Speaker Conversation)**: Casual, low-latency, streaming conversation. The user selects a topic *before* the session begins. The LLM native speaker strictly stays within that chosen topic for the entire conversation to maintain context stability.
- **Module 3 (Flashcards & Error Breakdown)**: Immediately following Modules 1 and 2, the app displays a waiting screen while the LLM dynamically generates flashcards from the session's errors. These flashcards are streamed to the UI one by one until a final "completion tag" is received. Once finished, the user reviews their translation errors and the newly generated flashcards.
- **Module 4 (Phrases & Vocabulary)**: SRS algorithm (e.g., SuperMemo-2) for learning vocabulary and sentence structures. The source data for phrases will strictly utilize established, curated datasets to prevent LLM hallucinations. Sources include DailyDialog and EmpatheticDialogues (for everyday context and reactions), Academic Phrasebank and FrameNet (for professional/structural formulation), and Tatoeba or Gutenberg Corpus (for grammatical correctness and complex sentence databases).
- **Module 5 (Progress & Performance Dashboard)**: Standalone module tracking grammar errors, conversational fluency, vocabulary adoption rate, historical trends, and providing actionable recommendations.

# 6. Proposed Provisional Features & Milestones (Ordered)
1. **Phase 1: Local CLI/Web Scaffold & Model Selection Pipeline**
   - *Why & How*: Before touching mobile development, build a simple Python script or basic local Web UI that interfaces with FastAPI, Faster-Whisper, LM Studio, and TTS. Use this phase to benchmark various models against your 8GB VRAM limit and 40GB System RAM. Validate that the subjective latency and quality are acceptable before proceeding.
2. **Phase 2: Core Agent Mechanics & Streaming UX**
   - *Why & How*: Implement the Research Agent and Native Speaker loops in isolation using DSPy. Build the exact streaming flow (from conversation completion to flashcard generation) to validate that the backend can stream JSON/flashcard data correctly and end with a completion tag.
3. **Phase 3: Database & State Management**
   - *Why & How*: Integrate SQLite and ChromaDB. Validate that conversation history is successfully persisted and retrieved, and that the duplicate-article prevention logic works effectively during multiple simulated sessions.
4. **Phase 4: Async Evaluation Pipeline**
   - *Why & How*: Implement the dashboard aggregations and tracking metrics. Ensure these background tasks do not interfere with the active VRAM/RAM constraints during a user session.
5. **Phase 5: Mobile App Frontend Integration**
   - *Why & How*: Connect the React Native/Flutter frontend to the robust, fully tested backend. Implement dynamic Wi-Fi IP connection handling.

# 7. Known Unknowns & Open Questions
- Specific DSPy optimization strategies to be used.
- Which specific quantized LLMs and STT models provide the best latency/quality ratio within the 8GB AMD VRAM constraint.
- The exact mechanism for the mobile app to locate the laptop's IP address on changing Wi-Fi networks (mDNS vs Manual Entry).

