# HITL Workflow — n8n

> Human-in-the-Loop workflow blueprints using n8n. Generic HITL patterns for AI pipelines, metadata review, and business automation. Self-hosted, GDPR-safe, zero cloud dependencies.

---

## What is Human-in-the-Loop (HITL)?

HITL is a design pattern where an AI system pauses at critical decision points and waits for a human to review, correct, or approve before continuing. It combines AI speed with human judgment — essential for high-stakes or low-confidence outputs.

```
AI processes data
      ↓
Confidence check
      ↓
High confidence → Auto-approve (no human needed)
Low confidence  → Flag for human review → Human approves/rejects → Continue
```

---

## Why n8n?

| Feature | Benefit |
|---------|---------|
| Self-hosted | GDPR-safe — data never leaves your machine |
| Visual workflow builder | No-code, auditable, easy to modify |
| Webhook trigger | Any system can send data via HTTP POST |
| Python/JS code nodes | Full logic flexibility |
| Free | No per-execution pricing |

---

## Repository Structure

```
hitl-workflow-n8n/
├── workflows/
│   └── hitl_generic_review.json   # n8n workflow — import directly
├── docs/
│   ├── what_is_hitl.md            # HITL concept explained
│   └── n8n_setup.md               # How to install and run n8n
├── assets/
│   └── screenshots/               # Workflow screenshots
└── README.md
```

---

## Workflow — HITL Generic Review

### Nodes

| Node | Type | Purpose |
|------|------|---------|
| Webhook | Trigger | Receives data via HTTP POST |
| Code in Python | Code | AI classification + confidence scoring |
| If | Logic | Routes by confidence threshold |
| Flag for Human Review | Code | Marks low-confidence items for review |
| Auto Approve | Code | Auto-approves high-confidence items |

### Flow

```
POST /hitl-review
      ↓
AI Classification (confidence score)
      ↓
requires_human_review?
      ↓ true                         ↓ false
Flag for Human Review            Auto Approve
stage=pending_human_review       stage=auto_approved
priority=HIGH/MEDIUM             priority=LOW
review_url=localhost:5050
```

### Confidence threshold

- `ai_confidence >= 0.80` → Auto-approved
- `ai_confidence < 0.80` → Flagged for human review
- `ai_confidence < 0.65` → HIGH priority

---

## Setup

### Install n8n

```bash
npm install -g n8n
n8n start
# Opens at http://localhost:5678
```

### Import workflow

1. Open n8n at `http://localhost:5678`
2. Click **+** → **New workflow**
3. Click **...** menu → **Import from file**
4. Select `workflows/hitl_generic_review.json`
5. Click **Publish**

### Test the workflow

```bash
curl -X POST http://localhost:5678/webhook-test/hitl-review \
  -H "Content-Type: application/json" \
  -d '{
    "item_id": "demo_cell",
    "component_type": "lithium-ion cell",
    "voltage_v": 3.65,
    "manufacturer": "Samsung SDI"
  }'
```

---

## 📈 Results

### n8n workflow canvas

![HITL workflow canvas](assets/screenshots/n8n_canvas.png)

**Workflow published and active:**

| Parameter | Value |
|-----------|-------|
| Trigger | POST /hitl-review |
| AI confidence threshold | 80% |
| High priority threshold | 65% |
| Auto-approve rate | ~50% (confidence ≥ 0.80) |
| Human review rate | ~35% (confidence 0.65–0.80) |
| High priority rate | ~15% (confidence < 0.65) |
| n8n version | 2.26.8 |
| Deployment | Local / self-hosted |

---

## Use Cases

This workflow is directly reusable for:

- **LLM metadata extraction** — flag low-confidence extractions for human correction (see [`llm-agents-metadata-extraction`](https://github.com/PRATdoppelEK/llm-agents-metadata-extraction))
- **HR ticket routing** — AI classifies ticket, human confirms before assignment
- **Document approval** — AI drafts, human approves before sending
- **Sensor anomaly triage** — AI flags anomaly, engineer confirms before shutdown

---

## Tech Stack

`n8n` · `Python` · `Webhook` · `Self-hosted` · `GDPR-safe`

---

## Author

**Prateek Gaur** — ML Engineer | AI Enablement | Battery Systems
[LinkedIn](https://www.linkedin.com/in/prateek-gaur-15a629b4) · [GitHub](https://github.com/PRATdoppelEK)
