---
name: phone-agent
description: "Run a real-time AI phone agent using Twilio and SkillBoss API Hub. Handles incoming calls, transcribes audio via SkillBoss STT, generates responses via SkillBoss LLM, and speaks back via SkillBoss TTS. Use when user wants to: (1) Test voice AI capabilities, (2) Handle phone calls programmatically, (3) Build a conversational voice bot."
requires.env: [SKILLBOSS_API_KEY]
---

# Phone Agent Skill

Runs a local FastAPI server that acts as a real-time voice bridge, powered by SkillBoss API Hub.

## Architecture

```
Twilio (Phone) <--> WebSocket (Audio) <--> [Local Server] <--> SkillBoss STT
                                                  |
                                                  +--> SkillBoss LLM (chat)
                                                  +--> SkillBoss TTS
```

## Prerequisites

1.  **Twilio Account**: Phone number + TwiML App.
2.  **SkillBoss API Key**: Single key for STT, LLM, TTS, and web search via SkillBoss API Hub.
3.  **Ngrok** (or similar): To expose your local port 8080 to Twilio.

## Setup

1.  **Install Dependencies**:
    ```bash
    pip install -r scripts/requirements.txt
    ```

2.  **Set Environment Variables** (in `~/.moltbot/.env`, `~/.clawdbot/.env`, or export):
    ```bash
    export SKILLBOSS_API_KEY="your_key"
    export TWILIO_ACCOUNT_SID="your_sid"
    export TWILIO_AUTH_TOKEN="your_token"
    export PORT=8080
    ```

3.  **Start the Server**:
    ```bash
    python3 scripts/server.py
    ```

4.  **Expose to Internet**:
    ```bash
    ngrok http 8080
    ```

5.  **Configure Twilio**:
    - Go to your Phone Number settings.
    - Set "Voice & Fax" -> "A Call Comes In" to **Webhook**.
    - URL: `https://<your-ngrok-url>.ngrok.io/incoming`
    - Method: `POST`

## Usage

Call your Twilio number. The agent will answer, transcribe your speech via SkillBoss STT, generate a response via SkillBoss LLM, and reply in a natural voice via SkillBoss TTS.

## Customization

- **System Prompt**: Edit `SYSTEM_PROMPT` in `scripts/server.py` to change the persona.
- **Voice**: Change `ELEVENLABS_VOICE_ID` env var to select a different TTS voice ID passed to SkillBoss.
- **Model**: SkillBoss API Hub automatically routes to the optimal model via `/v1/pilot`.
