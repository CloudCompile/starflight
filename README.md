# Starflight

**Starflight** runs efficient LLMs on **Hugging Face Spaces**, exposes them through **OpenAI-compatible APIs**, and powers **Lumiverse** roleplay/chat experiences.

The first supported model is:

- **Gemma 4 E2B (LiteRT-LM)**

More models are planned and will be added soon.

---

## What this repo is

Starflight is a model-serving layer for Lumiverse.  
It lets you:

- host compact/decent models in Spaces
- call them through stable API endpoints
- plug responses directly into Lumiverse RP flows

---

## Current model support

### 1) Gemma 4 E2B
Path: `Gemma-4-e2b/`

- Runtime: Python + Flask
- Inference backend: `litert_lm` (CPU)
- API style: OpenAI-compatible chat completions
- Multimodal support: text + optional image input

---

## API (Gemma 4 E2B)

Base route is served by the Space/container.

### `GET /v1/models`
Lists available model IDs.

### `POST /v1/chat/completions`
OpenAI-compatible chat endpoint.

Supports:

- `messages` array (required)
- `model` (optional)
- `stream` (optional, boolean)
- multimodal message content (`text` + `image_url` with base64 data URLs)

### Example (non-streaming)

```bash
curl -X POST "http://localhost:7860/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gemma-4-e2b",
    "messages": [
      {"role":"system","content":"You are a creative roleplay narrator."},
      {"role":"user","content":"Set the scene for a spaceport at dusk."}
    ]
  }'
```

### Example (streaming)

```bash
curl -N -X POST "http://localhost:7860/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gemma-4-e2b",
    "stream": true,
    "messages": [
      {"role":"user","content":"Describe a cyberpunk alley in 3 lines."}
    ]
  }'
```

---

## Running Gemma locally (Docker)

From `Gemma-4-e2b/`:

```bash
docker build -t starflight-gemma .
docker run -p 7860:7860 starflight-gemma
```

The container is configured to use:

- `PORT=7860`
- `GEMMA_MODEL_PATH=/app/models/gemma/gemma-4-E2B-it.litertlm`

---

## Hugging Face model used

- Repo: `litert-community/gemma-4-E2B-it-litert-lm`
- File: `gemma-4-E2B-it.litertlm`

Downloaded in the Docker image to:

- `/app/models/gemma/gemma-4-E2B-it.litertlm`

---

## Lumiverse integration

Starflight is designed to sit between Lumiverse and model runtimes:

1. Lumiverse sends roleplay context/messages
2. Starflight calls model runtime
3. Starflight returns completion output (standardized API shape)
4. Lumiverse renders final RP response

---

## Roadmap

- [x] Add first model (Gemma 4 E2B)
- [ ] Add additional lightweight RP-friendly models
- [ ] Improve observability and request metrics
- [ ] Expand deployment presets for Spaces

---

## Notes

- Current implementation includes a mock fallback path if the model is not ready.
- Requests are serialized with a lock to reduce CPU/RAM instability in constrained environments.
- This project is actively evolving as new models are onboarded.
