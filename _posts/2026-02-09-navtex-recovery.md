---
title: "NavtexRecovery: AI-Powered Restoration of Damaged Maritime Safety Messages"
description: "A Transformer-based system for real-time restoration of corrupted NAVTEX messages, with a Vue.js web interface and FastAPI backend."
author: Hoyeon Cho
date: 2026-02-09 09:00:00 +0900
categories: [Projects, Open Source]
tags: [navtex, pytorch, transformer, nlp, maritime-safety, vue, fastapi]
---

**NavtexRecovery** is an AI-powered restoration system for damaged NAVTEX (NAVigational TEleX) maritime safety messages. Built on a custom **ConstrainedCharBert** Transformer model, it provides real-time character-level error recovery with confidence scoring.

**GitHub**: [SemanticWave-Hoyeon/NavtexRecovery](https://github.com/SemanticWave-Hoyeon/NavtexRecovery)

---

## Screenshot

![NavtexRecovery Screenshot](/assets/img/navtex-recovery-screenshot.png)
_Web interface showing real-time NAVTEX message restoration with per-character confidence scores_

---

## Related Publication

> **Cho, H.; Lee, C.; Lee, S.** *Comparative Performance Analysis of Software-Based Restoration Techniques for NAVTEX Message.* ***J. Mar. Sci. Eng.*** **2025**, *13*, 1657.
> DOI: [10.3390/jmse13091657](https://doi.org/10.3390/jmse13091657) — **Cover Paper** (Sep 2025 Issue)

---

## Key Features

- **Real-time message restoration** — Corrupted characters (masked with `*`) are restored instantly
- **Per-character confidence scores** with alternative predictions
- **Adjustable confidence threshold** via slider
- **Batch restoration** support (up to 100 messages)
- **Automatic GPU/CPU detection**
- **Intuitive web interface** with dark theme

---

## Model Performance

| Category | Accuracy |
|----------|----------|
| **Overall** | ~75% |
| Alphabet (A-Z) | 91.1% |
| Space | 95.0% |
| Special Characters | 85.0% |
| Digits (0-9) | 44.6% |

---

## System Architecture

```
+-----------------+      HTTP/REST API      +-----------------+
|                 | <---------------------> |                 |
|  Vue.js 3       |                         |  FastAPI        |
|  Frontend       |    JSON (Request/       |  Backend        |
|                 |     Response)           |                 |
|  - RestoreForm  |                         |  - Model Loader |
|  - ResultView   |                         |  - Restoration  |
|                 |                         |    Service      |
+-----------------+                         +--------+--------+
                                                     |
                                                     v
                                           +-----------------+
                                           | ConstrainedChar |
                                           | BERT Model      |
                                           | (89MB, PyTorch) |
                                           +-----------------+
```

---

## Model Architecture

- **Architecture**: ConstrainedCharBert (Transformer-based)
- **Parameters**: ~23M
- **Model Size**: 89MB
- **Input**: Single character tokens (vocab_size: 57)
- **Max Sequence Length**: 1024
- **Config**: d_model=512, num_heads=5, depth=6, Rotary Position Embedding, Flash Attention, RMSNorm

---

## API Example

### Request

```bash
curl -X POST "http://localhost:8000/api/v1/restore" \
  -H "Content-Type: application/json" \
  -d '{
    "corrupted_text": "ZCZC NA*8 171522 UT* SEP*3",
    "return_confidence": true,
    "min_confidence": 0.7
  }'
```

### Response

```json
{
  "corrupted_text": "ZCZC NA*8 171522 UT* SEP*3",
  "restored_text": "ZCZC NA08 171522 UTC SEP23",
  "restorations": [
    {
      "position": 7,
      "original_char": "*",
      "restored_char": "0",
      "confidence": 0.9523,
      "alternatives": [
        {"char": "8", "confidence": 0.0312},
        {"char": "O", "confidence": 0.0089}
      ]
    }
  ],
  "avg_confidence": 0.8756,
  "mask_count": 3,
  "computation_time_ms": 45.2
}
```

---

## Tech Stack

| Layer | Technologies |
|-------|-------------|
| **Backend** | Python 3.10+, PyTorch 2.0+, FastAPI, x-transformers |
| **Frontend** | Vue.js 3, TypeScript, Vite, Axios |
| **Model** | ConstrainedCharBert (custom Transformer encoder) |

---

## License

PolyForm Noncommercial License 1.0.0 — Free for research, education, and non-commercial use. Commercial use requires a separate licensing agreement.
