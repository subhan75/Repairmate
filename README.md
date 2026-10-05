# Repairmate: AI-Powered Appliance Diagnostics System

> AI agents that help users diagnose, understand, and troubleshoot household appliances using videos, images, and natural language.

---

## Source Code Availability

⚠️ This repository does **not** contain the original code implementation.

The project was developed as part of an academic research effort and the source code is not publicly shareable.

This repository serves as a technical overview of the system architecture, design decisions, evaluation methodology, and my contributions to the project.

---

# Overview

RepairMate is a multimodal AI system that helps users troubleshoot household appliances without requiring technical expertise.

Instead of manually searching through PDFs, YouTube videos, forums, and repair guides, users can upload:

- Appliance photos
- Repair videos
- Text descriptions

The system analyzes the input, identifies potential issues, retrieves relevant information, and generates step-by-step troubleshooting guidance.

The project was built around a multi-agent architecture , where specialized agents collaborate through a centralized orchestrator. The original project was developed at UC Santa Cruz as part of a research-driven capstone effort. 

---

# Problem

Most appliance troubleshooting experiences look like this:

1. Search Google
2. Open 10 forum threads
3. Watch several YouTube videos
4. Read a manual
5. Hope one of them matches the problem

This process is slow, fragmented, and difficult for non-technical users.

RepairMate attempts to simplify this workflow by combining:

- Computer Vision
- Speech Recognition
- Retrieval-Augmented Generation
- Multi-Agent Reasoning
- Conversational AI

into a single troubleshooting experience. 

---

# System Architecture

```text
                    ┌─────────────────┐
                    │      User       │
                    └────────┬────────┘
                             │
                Text / Image / Video
                             │
                             ▼
                ┌─────────────────────┐
                │  Central Orchestrator│
                └───────┬─────────────┘
                        │
        ┌───────────────┼────────────────┐
        │               │                │
        ▼               ▼                ▼

 Video Agent      Knowledge Agent   Human Feedback

        │               │
        ▼               ▼

 YouTube Agent     Web Research Agent

        │
        ▼

 Recommendation Agent

        │
        ▼

 Step-by-Step Repair Guidance
```

All agents communicate through a central orchestrator which is responsible for task routing, context management, and response synthesis. This design improves modularity, debugging, and extensibility. 

---

# Core Components

## 1. Orchestrator Agent

The orchestrator acts as the brain of the system.

Responsibilities:

- Parse user requests
- Decide which agents to invoke
- Manage shared context
- Combine outputs into final responses
- Handle fallback and clarification workflows

Rather than a single monolithic prompt, the system uses specialized agents coordinated through the orchestrator. 

---

## 2. Video & Image Analysis Agent

This component processes uploaded images and repair videos.

Pipeline:

```text
Video
  │
  ▼
Frame Extraction
(1 FPS)
  │
  ▼
Audio Extraction
  │
  ▼
Whisper Transcription
  │
  ▼
Vision-Language Model
  │
  ▼
Diagnostic Summary
```

Key capabilities:

- Appliance identification
- Fault detection
- Symptom extraction
- Technical summarization
- Temporal reasoning across videos

The system combines visual frames with audio transcripts to produce concise diagnostic summaries optimized for troubleshooting workflows. 

### Models Evaluated

- Qwen 2.5 VL
- BLIP
- CLIP
- LLaVA
- Gemma

The final implementation primarily used Qwen 2.5 VL(Quantized to 4 bit for edge device performance) for visual understanding and Whisper for audio transcription. 

---

## 3. Knowledge Retrieval Agent

The retrieval layer provides factual grounding.

Sources include:

- Appliance manuals
- Repair documentation
- Technical guides
- Web search results

Key technologies:

- GPT-4o
- ColPali
- Tavily

A major challenge was retrieving information from highly visual appliance manuals containing diagrams, flowcharts, and structured layouts rather than plain text. ColPali was used to improve visual document retrieval capabilities. 

---

## 4. Web Research Agent

The WebThinker component performs:

- Query expansion
- Multi-step research
- Citation generation
- Result synthesis

This allows the system to gather up-to-date troubleshooting information while reducing hallucinations through retrieval grounding. 

---

## 5. YouTube Retrieval Agent

Many appliance repairs are easier to understand visually.

This component:

1. Generates repair instructions
2. Finds relevant repair videos
3. Retrieves transcripts
4. Ranks videos using semantic similarity

Architecture:

```text
Repair Solution
       │
       ▼
Bi-Encoder Retrieval
       │
       ▼
Cross-Encoder Re-ranking
       │
       ▼
Top Repair Video
```

Models:

- all-MiniLM-L6-v2
- ms-marco-MiniLM-L6-v2
- OpenAI embeddings

The retrieval system was fine-tuned using synthetic appliance repair datasets. 

---

## 6. Recommendation Agent

The recommendation system extracts entities from troubleshooting outputs and recommends:

- Replacement parts
- Repair tools
- Consumables
- Supporting materials

The goal was to reduce friction between diagnosis and repair execution. :chatgpt-content-reference{index="9"}

---

# Critic-Guided Orchestration

One of the more interesting aspects of the project was the orchestrator training strategy.

Instead of fine-tuning model weights, we implemented a Critic Agent that monitored orchestration behavior and injected corrective reasoning patterns.

Example:

```text
IF visual evidence is missing
THEN consult Video Agent

IF confidence is low
THEN ask user clarification questions

IF troubleshooting reaches advanced repair
THEN retrieve instructional videos
```

This allowed us to improve orchestration behavior through in-context learning rather than expensive model retraining. 

---

# Evaluation

The system was evaluated on appliance repair videos spanning:

- Refrigerators
- Dishwashers
- Washing machines
- Microwaves
- Ovens
- Water filters
- Monitors
- Heaters

Metrics included:

- BERTScore
- ROUGE-L
- GPT-4o Judge Evaluation

Experiments also explored:

- Prompt optimization using GEPA
- Audio vs vision-only ablations
- Vision model comparisons

Results showed that audio transcripts contributed significantly to troubleshooting quality, demonstrating the importance of multimodal reasoning in appliance diagnostics. :chatgpt-content-reference{index="11"}

---

# Key Technical Learnings

### Multi-Agent Systems

Specialized agents produced more reliable behavior than a single monolithic workflow.

### Multimodal AI

Combining audio and visual context dramatically improved diagnostic quality.

### Retrieval Matters

Grounding responses through manuals, documentation, and web search reduced hallucinations.

### Orchestration Is Hard

Many failures came from coordination issues between agents rather than model capability limitations.

---

# My Contributions

Key areas I contributed to:

- Multi-agent system design
- Video understanding workflows
- Vision-language model evaluation
- Prompt engineering
- Orchestrator architecture
- Diagnostic summarization pipelines
- Retrieval system integration
- System evaluation and experimentation

---

# Future Work

Potential future directions include:

- Real-time mobile camera streaming
- Better part detection
- Local/on-device inference
- Improved orchestration policies
- Larger-scale evaluation datasets
- Enhanced recommendation systems
- Integration with professional repair services

---

# Closing Thoughts

RepairMate explored how multimodal AI and multi-agent systems can move beyond chat interfaces and assist users with real-world troubleshooting tasks.

The project reinforced an important lesson:

Building useful AI systems is often less about choosing the best model and more about designing reliable orchestration, retrieval, evaluation, and user feedback loops around those models.
