# Startup Autopsy — Agentic Workflow (n8n)

An AI-powered startup evaluation system built in n8n that analyzes a startup idea across multiple dimensions and produces a structured verdict report with confidence scoring.

---

## Problem Statement

**Who is the user?** Early-stage founders and student entrepreneurs who want structured, unbiased feedback on their startup idea before investing time and money.

**What pain point does this solve?** Getting honest, structured feedback on a startup idea is hard. Mentors are busy, feedback is often vague, and founders tend to be blind to their own assumptions and market risks.

**Why does it matter?** Most startups fail due to poor problem-market fit, unvalidated assumptions, or ignoring competition. Early structured analysis can prevent wasted effort.

**What does the workflow produce?** A structured autopsy report including a verdict (Promising / Needs Work / Pivot), scores across clarity, market, and competition dimensions, confidence rating, key risks, riskiest assumptions, and concrete next steps.

---

## Workflow Overview

```
Startup Evaluation Form
        ↓
Input Validator (deterministic)
        ↓
┌──────────────────────────────┐
│  Problem Clarity Agent (AI)  │
│  Market Research Agent (AI)  │
│  Competition Checker Agent (AI) │
└──────────────────────────────┘
        ↓
Merge Analysis
        ↓
Scoring Logic (deterministic code)
        ↓
Verdict Router (deterministic: Promising / Needs Work / Pivot)
        ↓
┌────────────────────────────────────┐
│  Reasoning Reconstructor (AI)      │
│  Assumption Extractor (AI)         │  ← run based on verdict path
│  Error Detector (AI)               │
└────────────────────────────────────┘
        ↓
Merge Autopsy Results
        ↓
Confidence Evaluator (deterministic code)
        ↓
Deterministic Guard (threshold check)
        ↓
Report Generator (AI)
        ↓
Format Final Report
```

---

## AI vs Deterministic Steps

| Node | Type | Role |
|------|------|------|
| Problem Clarity Agent | **AI** | Evaluates how well-defined the problem statement is. Returns score + feedback. |
| Market Research Agent | **AI** | Assesses market size, growth trend, and opportunity. Returns score + market_size. |
| Competition Checker Agent | **AI** | Identifies competitors, moat, and competitive risk. Returns score + top_competitors. |
| Reasoning Reconstructor | **AI** | Reconstructs the reasoning chain used by agents to check for logical gaps. |
| Assumption Extractor | **AI** | Identifies hidden assumptions in the startup pitch that could invalidate the idea. |
| Error Detector | **AI** | Flags hallucinations, unsupported claims, or logical errors in agent outputs. |
| Report Generator | **AI** | Synthesizes all signals into a human-readable narrative report. |
| Input Validator | **Deterministic** | Checks that required fields (name, problem, solution, market, revenue model) are present. Rejects incomplete submissions. |
| Scoring Logic | **Deterministic** | Averages scores from 3 agents. Computes verdict threshold (≥7 = Promising, ≥5 = Needs Work, else Pivot). |
| Verdict Router | **Deterministic** | Routes workflow to different agent combinations based on verdict. |
| Confidence Evaluator | **Deterministic** | Penalizes confidence score based on errors found, hallucination risk, and risky assumptions. |
| Deterministic Guard | **Deterministic** | Final safety check — blocks report generation if confidence is critically low. |

---

## Tools & Integrations

- **n8n Form Trigger** — collects startup submission
- **Google Gemini / Claude API** — powers all AI reasoning nodes via n8n AI Agent nodes
- **Code nodes (JavaScript)** — scoring logic, confidence evaluation, routing thresholds
- **Merge nodes** — synchronizes parallel agent outputs before aggregation
- **Verdict Router** — rules-based branching on workflow state

---

## Sample Input / Output

**Input:**
```
Startup Name: EduTrack
Problem: Students can't track learning progress across multiple courses
Solution: AI dashboard aggregating progress from all learning platforms
Target Market: College students in India
Revenue Model: SaaS subscription
```

**Output:**
```json
{
  "verdict": "Promising",
  "scores": {
    "clarity": 7,
    "market": 8,
    "competition": 7
  },
  "confidence": 0.68,
  "key_strengths": [
    "Clearly identifies target users and pain points",
    "AI-driven insights offer competitive advantage"
  ],
  "key_risks": [
    "Dependence on users' willingness to pay",
    "Moderate competitive landscape with established players"
  ],
  "riskiest_assumption": "Target users prioritizing learning progress tracking will pay for a solution",
  "next_steps": [
    "Refine problem statement by detailing consequences",
    "Conduct market research to validate willingness to pay",
    "Develop marketing strategy emphasizing differentiation"
  ]
}
```

---

## Agentic Practices Demonstrated

- **Role definition** — each AI node has a specific responsibility (extractor, classifier, reviewer, scorer)
- **Structured outputs** — all AI nodes return JSON with defined schema (score, feedback, etc.)
- **Tool use** — form trigger, merge nodes, HTTP-based AI agents
- **Routing/branching** — Verdict Router directs flow based on scoring outcome
- **Deterministic control** — thresholds, validation, and scoring handled in code, not AI
- **Fallback handling** — Input Validator rejects bad input; Confidence Evaluator handles missing agent outputs gracefully
- **Human-in-the-loop (design)** — Deterministic Guard acts as a gate; Human Review node architected for high-risk cases

---

## Limitations & Future Improvements

- Human Review Wait node is currently bypassed for demo purposes; in production it would pause for mentor/admin approval on low-confidence verdicts
- Agent prompts could be further tuned with few-shot examples for more consistent JSON output
- Could add a Google Sheets logging node to track all submissions over time
- Email notification to founder with the report could be added as a final step

---

## Repo Contents

```
/
├── README.md
├── startupautopsy_workflow.json   # Exported n8n workflow JSON
├── sample_input.json              # Test input used in demo
└── sample_output.json             # Output produced by workflow
```
- Verdict Router branching rules and Deterministic Guard threshold logic
- Debugging JSON parsing issues across Claude API response objects
- End-to-end testing with the EduTrack sample input
