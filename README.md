# Multilingual AI Abuse Detection System

A robust, context-aware moderation system for detecting and masking abusive language in citizen complaint platforms across English, Hindi, Hinglish, and Odia.

## Overview

This system implements a hybrid approach to content moderation that goes beyond traditional single-model solutions. By combining multiple detection layers, it achieves high accuracy while preserving the original context and structure of user submissions.

### How It Works

The system employs a three-stage pipeline:

1. **Initial Toxicity Screening** - Hugging Face's toxic-bert model evaluates overall toxicity levels to determine if deeper analysis is warranted.

2. **Precise Phrase Extraction** - When toxicity is detected, Groq's LLaMA-3.3-70B model identifies the exact abusive phrases within the text.

3. **Intelligent Masking** - A custom span masking engine replaces only the identified abusive content, preserving sentence structure and non-abusive context.

This approach ensures that legitimate complaints containing abusive language are still processed rather than rejected outright, making it ideal for public service platforms.

## Key Capabilities

- Multilingual support for English, Hindi, Hinglish (Romanized Hindi), and basic Odia
- Granular phrase-level detection rather than sentence-level censorship
- Context preservation for downstream processing
- Built entirely on free and open-source technologies
- Lightweight architecture suitable for cloud or on-premises deployment

## Technology Stack

| Component | Technology |
|-----------|-----------|
| API Framework | FastAPI |
| Language Model | Groq LLaMA-3.3-70B Versatile |
| Toxicity Detection | Hugging Face unitary/toxic-bert |
| Runtime | Uvicorn |
| Supported Languages | English, Hindi, Hinglish, Odia |

## Project Structure
```
SIH-ABUSE_DETECTOR_MODEL/
│
├── app/
│   ├── api/
│   │   └── moderation_route.py
│   ├── models/
│   │   └── schemas.py
│   ├── services/
│   │   ├── pipeline.py
│   │   ├── toxicity_api.py
│   │   └── llm_extractor.py
│   ├── utils/
│   └── config.py  
│   ├── main.py
├
|── requirements.txt
|── .gitignore
|── LICENSE
```

## Setup Instructions

### 1. Environment Preparation

Create and activate a Python 3.10 virtual environment:
```bash
conda create -n Abuse_AI python=3.10
conda activate Abuse_AI
```

### 2. Install Dependencies
```bash
pip install -r requirements.txt
```

### 3. Configure API Keys

Create a `.env` file in the project root with your API credentials:
```
HF_API_KEY=your_huggingface_key
GROQ_API_KEY=your_groq_key
GROQ_MODEL=llama-3.3-70b-versatile
```

**Important:** Never commit the `.env` file to version control.

## Running the Application

Start the server from the project root directory:
```bash
uvicorn app.main:app --reload --port 8001
```

## API Reference

### Moderation Endpoint

**Endpoint:** `POST /api/v1/moderate`

**Request Format:**
```json
{
  "text": "hey u motherfucker tell me how to register"
}
```

**Response Format (Abuse Detected):**
```json
{
  "has_abuse": true,
  "original_text": "hey u IDIOT tell me how to register",
  "clean_text": "hey u ****** tell me how to register",
  "severity": "high",
  "flagged_spans": [
    {
      "start": 6,
      "end": 19,
      "original": "idiot",
      "masked": "******",
      "lang": "en",
      "category": "abuse",
      "severity": "low",
      "confidence": 0.97
    }
  ]
}
```

**Response Format (Clean Content):**
```json
{
  "has_abuse": false,
  "original_text": "please resolve my complaint soon",
  "clean_text": "please resolve my complaint soon",
  "severity": "none",
  "flagged_spans": []
}
```

### Response Fields

- `has_abuse`: Boolean indicating whether abusive content was detected
- `original_text`: Unmodified input text
- `clean_text`: Text with abusive phrases masked
- `severity`: Classification of abuse level (none, low, medium, high)
- `flagged_spans`: Array of detected abusive phrases with metadata including position, language, category, and confidence score

## Use Cases

This system is particularly well-suited for:

- Government grievance portals and complaint management systems
- Public forum moderation where context preservation is critical
- Multi-lingual content platforms requiring consistent policy enforcement
- Systems where complete message rejection is undesirable

## License

This project uses open-source components. Please review individual component licenses for specific terms.