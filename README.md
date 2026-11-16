# Multi-Agent Building Emergency Response System (CrewAI)

A multi-agent LLM workflow built with [CrewAI](https://www.crewai.com/) that simulates how a building's HVAC, fire, police and ambulance teams coordinate during emergencies. Each agent reads the earlier agents' reports before acting, so the final plan reflects all four perspectives.

**Keywords:** multi-agent systems, CrewAI, agentic AI, LLM agents, sequential orchestration, inter-agent communication, emergency response simulation, Llama 3.1, Hugging Face, Python.

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

Saved outputs are in `outputs/` (`scenario_1_output.txt`, `scenario_2_output.txt`).

## Stack

Python, CrewAI, LiteLLM, Hugging Face Inference API (Llama 3.1 8B Instruct via the Cerebras provider), Google Colab.

## How it works (theory)

**Agents as role-conditioned LLMs.** All four agents share the same model (Llama 3.1 8B Instruct). What makes them different is their *role*, *goal* and *backstory*, which are added to the prompt and steer the model to answer from that specialist's point of view. There are no real sensors or tools behind the agents: HVAC "readings" are written by the model from the incident text.

**Sequential orchestration.** CrewAI's `Process.sequential` runs the tasks in a fixed order. This fits emergencies that have a dependency chain: air quality affects which routes are safe (HVAC to Fire), safe routes decide the evacuation plan (Fire to Police), and the plan decides how medics reach patients (Police to Ambulance). The alternative, a hierarchical process with a manager agent that delegates and re-plans, adds flexibility but also more model calls and more places to fail.

**Inter-agent communication through task context.** Agents do not chat with each other. Each task lists earlier tasks in `context=[...]`, and CrewAI puts their outputs into the later agent's prompt:

```python
task_fire      = Task(..., context=[task_hvac])
task_police    = Task(..., context=[task_hvac, task_fire])
task_ambulance = Task(..., context=[task_hvac, task_fire, task_police])
```

This is a one-way blackboard-style pipeline. It is simple and easy to inspect, but a later agent cannot send a correction back to an earlier one.

**Generation settings.** Temperature is 0.3 and output is capped at 1,024 tokens per task, which keeps reports fairly consistent and short, though a long context can leave less room for a complete answer.

## Run it

1. Open `building_emergency_crewai.ipynb` in Google Colab.
2. Add your Hugging Face token as a Colab secret named `HF_TOKEN`.
3. Run the cells in order.

## Docs

- `docs/system_design_document.txt` - architecture, agent definitions, design decisions
- `docs/reflection_report.txt` - what worked, challenges, learnings

## Limitations

- Agents cannot read real data or call tools, so every number in the reports is generated text and can be inconsistent between agents.
- The flow is one-way: no agent can challenge or correct an earlier agent's report.
- Only two scenarios were run, with no quantitative evaluation. Quality judgements in `docs/reflection_report.txt` are qualitative.
- An 8B model is the only one tested.

## Notes

Agent outputs are LLM-generated simulations from an 8B model and are not real emergency guidance. The model's reports can be generic or inconsistent (for example, repeating treatment steps across patients).
