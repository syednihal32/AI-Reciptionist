# Quick Start Guide

Get the AI Voice Receptionist Agent running in 5 minutes!

## Prerequisites

- Python 3.11+
- pip
- Groq API Key (free at https://console.groq.com/keys)

## Step 1: Get Groq API Key (1 minute)

1. Go to https://console.groq.com/keys
2. Sign up (free account)
3. Create an API key
4. Copy the key

## Step 2: Setup Project (2 minutes)

```bash
# Navigate to project directory
cd ai_voice_receptionist

# Create virtual environment
python -m venv venv

# Activate virtual environment
# Windows:
venv\Scripts\activate
# macOS/Linux:
source venv/bin/activate

# Create .env file
cp .env.example .env

# Edit .env and add your Groq API key
# Open .env in your editor and replace:
# GROQ_API_KEY=your_groq_api_key_here
# with your actual key
```

## Step 3: Install Dependencies (1 minute)

```bash
# Install backend dependencies
pip install -r backend/requirements.txt

# Install frontend dependencies
pip install -r frontend/requirements.txt
```

## Step 4: Run Backend (30 seconds)

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

## Step 5: Run Frontend (30 seconds)

Open a new terminal and:

```bash
# Activate virtual environment (if not already active)
# Windows: venv\Scripts\activate
# macOS/Linux: source venv/bin/activate

# Run Streamlit
streamlit run frontend/app.py
```

Streamlit will open in your browser at `http://localhost:8501`

## Step 6: Test It!

1. In the Streamlit UI, check the sidebar – it should show "✓ Backend Online"
2. Upload an audio file (WAV or MP3)
3. Click "🎯 Process Call"
4. See the transcription, AI reply, and listen to the response!

## Troubleshooting

### "GROQ_API_KEY not set"
- Make sure you added your key to `.env`
- Restart the backend after editing `.env`

### "Backend Offline"
- Make sure backend is running on `http://localhost:8000`
- Check that no firewall is blocking port 8000

### "No speech detected"
- Use a different audio file with clearer speech
- Make sure audio file is not corrupted

## Next Steps

- Read `README.md` for full documentation
- Check `docs/ARCHITECTURE.md` for technical details
- See `docs/DEMO_FLOW.md` for presentation guide
- Explore the code in `backend/` and `frontend/`

## Sample Audio Files

To test, you can:
1. Record yourself speaking using your phone or computer
2. Use any WAV/MP3 file with speech
3. Try saying things like:
   - "I'd like to schedule an appointment"
   - "Can you check my order status?"
   - "I need technical support"

Enjoy! 🎤
