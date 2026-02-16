
# Gemma on Google Cloud Run with GPU

Deploy Google's Gemma 3 (4B) language model on Google Cloud Run using Ollama with GPU acceleration (NVIDIA L4).


## Features

- GPU-accelerated inference using NVIDIA L4
- Streaming and non-streaming response modes
- Model weights loaded into the container image
- Always-on model (never unloaded from GPU memory) >> faster response 
- REST based API compatible with OpenAI 

## Prerequisites

- Google Cloud account with billing enabled
- `gcloud` CLI installed and configured
- Docker installed (for local testing)

## Quick Start


### 1. Set Your Project ID

```bash
export PROJECT_ID=your-google-cloud-project-id
gcloud config set project $PROJECT_ID
```

### 2. Enable Required APIs

```bash
gcloud services enable run.googleapis.com
gcloud services enable cloudbuild.googleapis.com
gcloud services enable artifactregistry.googleapis.com
```

### 3. Deploy to Cloud Run

```bash
gcloud run deploy gemma-ollama \
  --source . \
  --platform managed \
  --region us-central1 \
  --allow-unauthenticated \
  --memory 16Gi \
  --cpu 4 \
  --gpu 1 \
  --gpu-type nvidia-l4 \
  --port 8080 \
  --no-cpu-throttling
```

## Usage

### Get Your Service URL

```bash
gcloud run services describe gemma-ollama \
  --region us-central1 \
  --format 'value(status.url)'
```

### Streaming Response 

```bash
curl -X POST https://your-service-url/api/generate \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gemma3:4b",
    "prompt": "what is the history of artificial intellignece"
  }'
```

### Non-Streaming Response

```bash
curl -X POST https://your-service-url/api/generate \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gemma3:4b",
    "prompt": "what is the history of artificial intellignece",
    "stream": false
  }'
```

### Chat Completion API

```bash
curl -X POST https://your-service-url/api/chat \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gemma3:4b",
    "messages": [
      {
        "role": "user",
        "content": "What is the capital of saudi arabia?"
      }
    ],
    "stream": false
  }'
```
