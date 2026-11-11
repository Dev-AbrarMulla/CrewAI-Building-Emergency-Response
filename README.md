# Multi-Agent Building Emergency Response System (CrewAI)

A multi-agent workflow built with [CrewAI](https://www.crewai.com/) that simulates how a building's response teams coordinate during emergencies. Course project for the Large Language Models course at BITS Pilani, Dubai Campus.

## Overview

Four role-specific agents handle each incident in sequence:

| Agent | Role |
|---|---|
| HVAC | Reads sensor data, reports air quality, recommends ventilation actions |
| Fire | Assesses hazards, identifies safe evacuation routes |
| Police | Coordinates evacuation and the security response |
| Ambulance | Triages patients, confirms access routes, notifies hospitals |

Each agent has a role, a goal, and a backstory. The process is **sequential**: every task receives the earlier agents' reports through CrewAI's task `context`, so each agent builds on the previous findings.

```
Incident report -> HVAC -> Fire -> Police -> Ambulance -> Final report
```

## Scenarios

1. **Fire + medical emergency** (smoke detected, unconscious person in the lobby)
2. **Security breach + gas leak** (unauthorised access, CO2 spike, jammed exit)

Saved outputs are in `outputs/`.

## Stack

Python, CrewAI, LiteLLM, Hugging Face Inference API (Llama 3.1 8B Instruct via the Cerebras provider), Google Colab.

## Run it

1. Open `building_emergency_crewai.ipynb` in Google Colab.
2. Add your Hugging Face token as a Colab secret named `HF_TOKEN`.
3. Run the cells in order.

## Docs

- `docs/system_design_document.txt` - architecture, agent definitions, design decisions
- `docs/reflection_report.txt` - what worked, challenges, learnings

## Notes

Agent outputs are LLM-generated simulations from an 8B model and are not real emergency guidance. The model's reports can be generic or inconsistent (for example, repeating treatment steps across patients).
