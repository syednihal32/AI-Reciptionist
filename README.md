# 🎤 AI Voice Receptionist Agent – Audio Call Simulator

**A smart, production-ready AI receptionist that processes voice calls, understands intent, and generates intelligent responses. Designed for easy integration with Twilio for real phone calls.**

---

## 📋 Overview

This project implements an **AI-powered voice receptionist** that:

- **Accepts voice input** (simulated phone calls via audio file upload for now)
- **Transcribes speech to text** using local Whisper (faster-whisper)
- **Understands intent** using LLM (Groq Llama 3 or Google Gemini)
- **Generates smart replies** with a receptionist-style system prompt
- **Converts text to speech** using Edge-TTS or gTTS
- **Stores conversations** in SQLite for history and analytics
- **Provides a clean UI** with Streamlit for easy demo and interaction
- **Maintains Twilio-ready architecture** for future real phone integration

**Key Features:**
- ✅ Zero paid dependencies (all free/open-source)
- ✅ Local speech processing (no cloud STT required)
- ✅ Fast LLM inference via Groq free tier
- ✅ Modular, clean code architecture
- ✅ Easy to extend and customize
- ✅ Production-ready error handling

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    STREAMLIT FRONTEND                        │
│  (Audio Upload, Chat Display, Conversation History)         │
└────────────────────┬────────────────────────────────────────┘
                     │ HTTP (REST API)
                     ▼
┌─────────────────────────────────────────────────────────────┐
│                    FASTAPI BACKEND                           │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  /process_audio  →  STT → LLM → TTS → DB → Response │   │
│  │  /conversations  →  Query conversation history       │   │
│  │  /health         →  Health check                     │   │
│  └──────────────────────────────────────────────────────┘   │
└─────┬──────────────┬──────────────┬──────────────┬──────────┘
      │              │              │              │
      ▼              ▼              ▼              ▼
  ┌────────┐    ┌────────┐    ┌────────┐    ┌────────┐
  │Whisper │    │ Groq   │    │Edge-TTS│    │SQLite  │
  │  STT   │    │ LLM    │    │  TTS   │    │  DB    │
  └────────┘    └────────┘    └────────┘    └────────┘
```

**Data Flow:**
1. User uploads audio file via Streamlit
2. FastAPI receives file and saves temporarily
3. **STT Module** (Whisper) transcribes audio → text
4. **LLM Module** (Groq) generates AI reply + intent
5. **TTS Module** (Edge-TTS) synthesizes reply → audio
6. **DB Module** (SQLAlchemy + SQLite) stores conversation
7. Streamlit displays results and plays audio response

**Future Twilio Integration:**
- Replace "file upload" with "Twilio webhook audio"
- Add `/twilio-webhook` endpoint (see `backend/twilio_stub.py`)
- Same processing pipeline, different audio source

---

## 🚀 Quick Start

### Prerequisites

- **Python 3.11+**
- **pip** (Python package manager)
- **Groq API Key** (free tier available at https://console.groq.com/keys)

### Installation

#### 1. Clone/Download the Project

```bash
cd ai_voice_receptionist
```

#### 2. Create Virtual Environment

```bash
# Windows
python -m venv venv
venv\Scripts\activate

# macOS/Linux
python3 -m venv venv
source venv/bin/activate
```

#### 3. Install Backend Dependencies

```bash
cd backend
pip install -r requirements.txt
cd ..
```

#### 4. Install Frontend Dependencies

```bash
cd frontend
pip install -r requirements.txt
cd ..
```

#### 5. Configure Environment

```bash
# Copy example config
cp .env.example .env

# Edit .env and add your Groq API key
# LLM_PROVIDER=groq
# GROQ_API_KEY=your_actual_key_here
```

Get your **Groq API Key** for free:
1. Go to https://console.groq.com/keys
2. Sign up (free account)
3. Create an API key
4. Copy it to `.env`

#### 6. Run Backend

```bash
# From project root
uvicorn backend.main:app --reload --host 0.0.0.0 --port 8000
```

You should see:
```
✓ Database initialized successfully.
✓ Backend initialized successfully.
Uvicorn running on http://0.0.0.0:8000
```

#### 7. Run Frontend (in a new terminal)

```bash
# Activate venv first
# Windows: venv\Scripts\activate
# macOS/Linux: source venv/bin/activate

streamlit run frontend/app.py
```

Streamlit will open in your browser at `http://localhost:8501`

---

## 📖 Usage

### Demo Flow

1. **Open Streamlit UI** at `http://localhost:8501`
2. **Check Backend Status** in sidebar (should show "✓ Backend Online")
3. **Upload Audio File**
   - Click "Choose an audio file"
   - Select a WAV or MP3 file
   - Preview plays in the UI
4. **Process Call**
   - Click "🎯 Process Call" button
   - Wait for processing (STT → LLM → TTS)
5. **View Results**
   - See transcription of what the caller said
   - See AI receptionist's reply
   - See inferred intent (scheduling, support, etc.)
   - Listen to the AI's voice response
6. **Check History**
   - Right panel shows recent conversations
   - Filter by intent if desired
   - Click "🔄 Refresh History" to update

### Example Calls to Try

**Scheduling Request:**
- "Hi, I'd like to schedule an appointment for next Tuesday at 2 PM."
- Expected intent: `scheduling`

**Order Status:**
- "Can you check on my order? Order number 12345."
- Expected intent: `order_status`

**Support Request:**
- "I'm having trouble logging into my account. Can you help?"
- Expected intent: `support`

**General Inquiry:**
- "What are your office hours?"
- Expected intent: `general`

---

## 🔌 API Endpoints

### Health Check
```
GET /health
```
Returns backend status and configuration.

### Process Audio
```
POST /process_audio
Content-Type: multipart/form-data

Body:
  file: <audio_file>

Response:
{
  "success": true,
  "transcription": "Hi, I'd like to schedule an appointment",
  "reply_text": "I'd be happy to help! What date works best for you?",
  "intent": "scheduling",
  "reply_audio_url": "/audio/reply_20231215_143022_abc123.mp3",
  "timestamp": "2023-12-15T14:30:22.123456"
}
```

### Get Conversations
```
GET /conversations?limit=20&intent=scheduling

Response:
{
  "success": true,
  "count": 5,
  "conversations": [
    {
      "id": 1,
      "user_text": "...",
      "ai_text": "...",
      "intent": "scheduling",
      "created_at": "2023-12-15T14:30:22"
    },
    ...
  ]
}
```

---

## 📁 Project Structure

```
ai_voice_receptionist/
├── backend/
│   ├── main.py              # FastAPI app & endpoints
│   ├── config.py            # Configuration management
│   ├── db.py                # Database & ORM
│   ├── stt.py               # Speech-to-Text (Whisper)
│   ├── tts.py               # Text-to-Speech (Edge-TTS/gTTS)
│   ├── llm_engine.py        # LLM (Groq/Gemini)
│   ├── twilio_stub.py       # Twilio integration placeholder
│   ├── utils.py             # Helper functions
│   ├── __init__.py
│   └── requirements.txt      # Backend dependencies
│
├── frontend/
│   ├── app.py               # Streamlit UI
│   └── requirements.txt      # Frontend dependencies
│
├── data/
│   ├── conversations.db     # SQLite database (auto-created)
│   ├── audio_responses/     # Generated TTS audio files
│   └── temp_audio/          # Temporary audio files
│
├── docs/
│   ├── ARCHITECTURE.md      # Detailed architecture
│   └── DEMO_FLOW.md         # Demo presentation guide
│
├── .env.example             # Environment configuration template
├── README.md                # This file
└── .gitignore               # Git ignore rules
```

---

## ⚙️ Configuration

All configuration is managed via `.env` file. Key variables:

| Variable | Default | Description |
|----------|---------|-------------|
| `LLM_PROVIDER` | `groq` | LLM provider: `groq` or `gemini` |
| `GROQ_API_KEY` | - | Your Groq API key (required) |
| `GROQ_MODEL` | `llama3-8b-instant` | Groq model to use |
| `COMPANY_NAME` | `Acme Corp` | Company name for receptionist |
| `DB_URL` | `sqlite:///./data/conversations.db` | Database URL |
| `AUDIO_OUTPUT_DIR` | `./data/audio_responses` | Where to save generated audio |
| `BACKEND_HOST` | `0.0.0.0` | Backend server host |
| `BACKEND_PORT` | `8000` | Backend server port |

---

## 🔮 Future Enhancements

### Phase 1: Real Phone Integration (Twilio)
- [ ] Implement `/twilio-webhook` endpoint
- [ ] Add Twilio SDK to requirements
- [ ] Configure Twilio phone number with webhook
- [ ] Test with real phone calls
- [ ] Add call recording and analytics

### Phase 2: Advanced Features
- [ ] Multi-turn conversations (context awareness)
- [ ] Appointment calendar integration
- [ ] CRM integration for customer data
- [ ] Sentiment analysis
- [ ] Call transfer to human agents
- [ ] Advanced intent classification (NLU)

### Phase 3: Optimization
- [ ] GPU acceleration for Whisper
- [ ] Model quantization for faster inference
- [ ] Caching for common responses
- [ ] Load balancing for multiple instances
- [ ] Advanced monitoring and logging

### Phase 4: Deployment
- [ ] Docker containerization
- [ ] Kubernetes orchestration
- [ ] CI/CD pipeline
- [ ] Production-grade logging
- [ ] Performance monitoring

---

## 🐛 Troubleshooting

### Backend won't start
**Error:** `GROQ_API_KEY not set`
- **Solution:** Add your Groq API key to `.env` file

**Error:** `Port 8000 already in use`
- **Solution:** Change `BACKEND_PORT` in `.env` or kill the process using port 8000

### Frontend can't connect to backend
**Error:** `Backend Offline` in sidebar
- **Solution:** Make sure backend is running on `http://localhost:8000`
- Check `BACKEND_BASE_URL` in `.env`

### Audio processing fails
**Error:** `No speech detected in audio file`
- **Solution:** Upload a file with clear speech, not silence or music

**Error:** `Transcription failed`
- **Solution:** Try a shorter audio file or ensure audio quality is good

### Out of memory
**Error:** `CUDA out of memory` or similar
- **Solution:** Whisper is using GPU. Set `device="cpu"` in `backend/stt.py`

---

## 📊 Performance Notes

- **STT (Whisper):** ~5-15 seconds for 30-second audio (CPU)
- **LLM (Groq):** ~1-2 seconds for reply generation
- **TTS (Edge-TTS):** ~1-3 seconds for audio synthesis
- **Total:** ~10-25 seconds per call (depends on audio length)

**Optimization Tips:**
- Use GPU for Whisper if available (set `device="cuda"`)
- Use smaller Whisper model (`tiny` or `base`) for speed
- Cache common responses
- Use async processing for multiple calls

---

## 📝 License

This project is created for the 48-Hour AI Agent Development Challenge.

---

## 🤝 Support

For issues or questions:
1. Check the `docs/` folder for detailed documentation
2. Review error messages in backend logs
3. Verify `.env` configuration
4. Check that all dependencies are installed

---

## 🎯 Key Takeaways

✅ **Zero-cost deployment** – All free/open-source tools
✅ **Production-ready** – Clean code, error handling, logging
✅ **Extensible** – Easy to add features or swap components
✅ **Twilio-ready** – Architecture supports real phone integration
✅ **Fast** – Groq provides fast LLM inference
✅ **Local** – No cloud dependencies for core processing

---

**Built with ❤️ for the 48-Hour AI Agent Development Challenge**
