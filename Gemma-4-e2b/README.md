---
title: Gemma 4 API
emoji: 🤖
colorFrom: blue
colorTo: green
sdk: docker
pinned: false
---

# Gemma 4 API

Flask REST API for [Gemma 4 E2B](https://huggingface.co/litert-community/gemma-4-E2B-it-litert-lm) running on-device via LiteRT-LM.

## Endpoints

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/gemma?ask=hello` | Text query |
| `POST` | `/gemma` | JSON `{ask, image?}` — text + optional base64 image |
| `POST` | `/gemma` | `multipart/form-data` — text + image file upload |
| `GET` | `/gemma/download` | Download model from HuggingFace into `models/gemma/` |
| `GET` | `/gemma/download?status=1` | Poll download progress |
| `GET` | `/health` | Health + model status |

## Setup

### Option 1 — Docker with model already downloaded

```bash
docker build -t gemma-api .
docker run -p 7860:7860 \
  -v /your/model/dir:/app/models/gemma \
  gemma-api
```

### Option 2 — Download model at runtime

```bash
docker build -t gemma-api .
docker run -p 7860:7860 gemma-api
# then hit:
curl http://localhost:7860/gemma/download
# poll until done:
curl http://localhost:7860/gemma/download?status=1
```

Model file: `gemma-4-E2B-it.litertlm` (~2.5 GB)  
Expected path inside container: `/app/models/gemma/gemma-4-E2B-it.litertlm`

## Example

```bash
# Text
curl "http://localhost:7860/gemma?ask=hello"

# Image (base64)
curl -X POST http://localhost:7860/gemma \
  -H "Content-Type: application/json" \
  -d '{"ask":"what is in this image?","image":"<base64>"}'

# Image (file upload)
curl -X POST http://localhost:7860/gemma \
  -F "ask=describe this" \
  -F "image=@photo.jpg"
```
