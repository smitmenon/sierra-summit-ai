# 🏔️ Sierra Summit AI  
### *Plan Smarter. Ski Safer - Your personal multi-agent Tahoe ski trip planner.*

---

## Overview

**Sierra Summit AI** is an intelligent, multi-agent winter travel planner that builds personalized ski itineraries for Lake Tahoe. It takes a free-form natural language request — such as:

> “Plan a budget-friendly 2-day ski trip next weekend for a beginner from San Jose with great views.”

…and produces a complete trip plan including:
- Resort & lodging recommendations  
- Road & weather insights  
- A morning/afternoon/evening itinerary  
- Safety considerations  
- A final natural-language summary  

This agent system uses **Google’s Agents Development Kit (ADK)** and demonstrates:
- Multi-agent orchestration  
- Tool calling (search, weather, resort lookup, lodging lookup)  
- Reasoning traces  
- Observability and logging  
- Lightweight evaluation  

This project was built as part of the **Kaggle × Google Agents Intensive Capstone Project**.

---

## 🎯 Problem Statement

Planning a Tahoe ski trip is surprisingly complex — conditions change rapidly, resorts vary dramatically, and lodging options depend on timing, skill level, and budget. Even as an experienced traveler, I found myself repeatedly researching the same things: weather, chain controls, driving risk, best resorts for skill level, and where to stay.

This inspired me to build **Sierra Summit AI**, an assistant that:
- Parses user preferences  
- Researches real-time travel conditions  
- Selects the right resort & lodging  
- Generates a simple friendly plan  
- Surfaces safety issues automatically  

---

## 🤖 Why Agents?

A ski trip requires many reasoning stages:
1. Parsing requirements  
2. Researching road & weather advisories  
3. Selecting resorts/lodging  
4. Generating itinerary  
5. Producing safety notes  

Each step is separable, tool-dependent, and benefits from specialized reasoning.  
A **multi-agent system** is the natural fit because:

- Each sub-agent can focus on a single responsibility  
- Tools (e.g., google_search, weather_tool) are called only by the right agent  
- The Supervisor reliably orchestrates the flow  
- Debug traces allow you to inspect each reasoning step  

---

## 🧩 Architecture

```
[ User Query ]
        |
        v
+--------------------------+
|  TripSupervisorAgent     |
|  (Orchestration Engine)  |
+--------------------------+
        |
        v
+------------------------------+
|  TripRequirementParserAgent  |
|  -> trip_request             |
+------------------------------+
        |
        v
+------------------------------+
|     TripSearchAgent          |
|   (calls google_search)      |
|  -> road_info                |
+------------------------------+
        |
        v
+----------------------------------------------+
|             TripContextAgent                  |
|  (resort_lookup, lodging_lookup, weather_tool)|
|        -> trip_context                        |
+----------------------------------------------+
        |
        v
+------------------------------+
|     TripItineraryAgent       |
|  -> itinerary (Markdown)     |
+------------------------------+
        |
        v
+------------------------------+
|      TripSafetyAgent         |
|     -> safety_notes          |
+------------------------------+
        |
        v
+------------------------------------------------+
|        Final Answer (Supervisor Output)        |
| - Summary                                      |
| - Markdown itinerary                           |
| - Safety notes                                 |
+------------------------------------------------+
        ^
        |
[ Observability & Evaluation ]
- Logging for tools and agent steps
- Debug mode trace
- Eval queries
```

---

## 🔧 Tools & Technologies

- **Google ADK** – Agent framework + orchestration  
- **Gemini 2.5 Flash** – Model for all agents  
- **google_search tool** – Real-world Tahoe road/weather info  
- **Custom Python tools** – weather, resort lookup, lodging lookup  
- **InMemoryRunner** – Debug & step-by-step tracing  
- **Python logging** – Observability  
- **Lightweight Evaluation** – Skill + budget correctness scoring  

---

## 🔍 Observability

This system includes minimal but effective observability:
- Each tool logs:
  - Arguments  
  - Duration  
  - Output highlights  
- Supervisor run logs:
  - Start/end of each run  
  - Number of events  
- `run_debug()` prints a clean reasoning trace  
- All logs print to notebook output (can be redirected to file easily)

---

## 🧪 Evaluation

A small evaluation suite checks:
- Whether the parsed **skill level** matches expectation  
- Whether **budget** is correctly interpreted  

Format:

```python
EVAL_QUERIES = [
    {
        "id": "reasonable_intermediate_less_crowded",
        "query": "...",
        "expected_skill": "intermediate",
        "expected_budget": "mid",
    },
    ...
]
```

Output is a clean table:

| id | skill_pass | budget_pass | score |
|----|-----------|-------------|--------|
| reasonable_intermediate_less_crowded | 1 | 1 | 1.0 |
| advanced_south | 1 | n/a | 1.0 |
| family_friendly | 1 | n/a | 1.0 |

---

## 🚀 Demo

To run a manual test:

```python
final_answer, steps = await run_tahoe_trip_debug(
    "Plan a cheap 2-day ski trip next weekend for a beginner from San Jose."
)
```

This prints:
- Step-by-step reasoning  
- Tool calls  
- Final itinerary  

---

## 🛠️ Setup Instructions

1. Clone the repository:
```bash
git clone https://github.com/smitmenon/sierra-summit-ai.git
cd sierra-summit-ai
```

2. Install dependencies:
```bash
pip install google-adk google-generativeai kaggle
```

3. Add your Gemini API key:
```python
os.environ["GOOGLE_API_KEY"] = "<your-key>"
```

4. Run the notebook.

---

## ✨ Future Improvements

If I had more time:
- Integrate real-time weather & lift-status APIs  
- Replace hard-coded tools with live data sources  
- Add fine-grained roadside safety analysis  
- Add caching for tool calls  
- Expand beyond Tahoe to all major ski regions  

---

## 📎 Attribution

Built by **Smitha Menon** for the **Kaggle × Google Agents Intensive Capstone Project**.
