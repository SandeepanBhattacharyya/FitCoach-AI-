# FitCoach AI 🏋️‍♂️⚡

> A conversational AI fitness and workout coaching assistant built with the Google Agent Development Kit (ADK), Vertex AI, Cloud Storage, Cloud Firestore, and Agent Platform Code Execution.

---

## 🌟 Overview

**FitCoach AI** is an intelligent fitness companion designed to create personalized workout plans, track exercise history, calculate key fitness metrics, generate exercise media, and recall user preferences across sessions. It features a responsive, dark-mode web chat interface powered by a FastAPI proxy and rich A2UI surface components.

---

## 🛠️ Implemented Tools & Capabilities

The code in this repository (`app/agent.py`) implements the following tools and capabilities:

### 1. 🧠 Memory & Personalization (ADK Memory Bank)
- **PreloadMemoryTool & Memory Callbacks**: Automatically stores and recalls long-term user context across sessions (e.g., fitness goals, injuries, personal records, preferred exercise styles).

### 2. 🗄️ Database & Catalog Management (Google Cloud Firestore)
- `log_workout`: Logs completed workout sessions, exercises, sets, reps, and weights into Firestore.
- `get_workout_history`: Retrieves historical workout logs filtered by date range or exercise.
- `get_exercise_catalog`: Fetches exercises stored in the Firestore exercise library.
- `search_back_friendly_exercises`: Queries exercises from Firestore filtered for back-friendly safety tags.
- `add_exercise_to_catalog`: Persists custom user-defined exercises to Firestore.

### 3. 🌐 External API Integration (RAG & Public Search)
- `search_public_exercises`: Queries the public Wger Workout Manager API for real exercise guides, equipment details, and target muscle groups.

### 4. 🧮 Calculators & Fitness Formulas
- `calculate_one_rep_max`: Computes 1-Rep Max (1RM) using Epley and Brzycki strength formulas.
- `calculate_daily_macros`: Calculates BMR, TDEE, and recommended daily macronutrient splits (protein/carbs/fats) based on user metrics and goals.

### 5. 🎨 AI Media Generation (Google GenAI SDK & Cloud Storage)
- `generate_exercise_image`: Uses `gemini-3.1-flash-lite-image` in the `global` region to generate high-quality exercise illustrations and workout diagrams.
- `generate_exercise_video`: Uses `gemini-omni-flash-preview` in the `global` region via the GenAI Interactions API (`response_modalities=["VIDEO"]`) to generate exercise demonstration clips.
- **Dual Storage Output**: Both media tools save generated files as ADK Playground artifacts (`tool_context.save_artifact`) and upload them directly to a public Google Cloud Storage bucket (`fitcoach-ai-media-...`).

### 6. 💻 Agent Platform Code Execution
- **AgentEngineSandboxCodeExecutor**: Securely executes Python code in Vertex Reasoning Engine / Agent Engine sandbox for complex calculations, analytics, and custom data processing.

### 7. 🎨 Rich UI & Dynamic Interface (A2UI Schema Manager)
- **A2UI Surface Rendering**: Uses `A2uiSchemaManager` and `a2ui_callback` to format response data parts into structured UI components (Cards, Columns, Rows, Text, Image, Video) in the frontend.

---

## ☁️ Google Cloud Services Used

- **Vertex AI / Gemini API**: `gemini-2.5-flash` (reasoning engine), `gemini-3.1-flash-lite-image` (image generation), and `gemini-omni-flash-preview` (video generation).
- **Google Cloud Firestore**: Persists user workout history and exercise catalog data.
- **Google Cloud Storage**: Hosts publicly accessible generated exercise images and demonstration videos.
- **Vertex AI Reasoning Engine / Agent Engine**: Hosts code execution sandbox and agent runtime.
- **Google Cloud Run**: Containerized deployment target for the frontend and agent proxy.

---

## 📂 Repository Structure

```text
fitcoach-ai/
├── app/
│   ├── __init__.py
│   ├── agent.py              # Root agent, tools, instruction, memory, and A2UI callbacks
│   └── a2ui_utils.py         # A2UI callback transformation helper
├── frontend/
│   ├── Dockerfile            # Cloud Run container configuration
│   ├── main.py               # FastAPI backend proxy for Agent Engine / ADK
│   └── static/
│       └── index.html        # Custom dark-theme web chat UI with prompt chips
├── agents-cli-manifest.yaml   # Deployment & project metadata manifest
├── demo.gif                  # Recorded video demo preview
└── README.md                 # Project documentation
```

---

## 🚀 Setup & Local Execution

### Prerequisites
- Python 3.11+
- `uv` or `pip`
- Google Cloud SDK (`gcloud`) configured with project access and credentials

### 1. Install Dependencies
```bash
uv sync
```

### 2. Run Agent Playground Locally
Start the ADK playground server to test the agent interactively:
```bash
agents-cli playground --port 8085
```

### 3. Run Frontend Server Locally
Navigate to the `frontend/` directory and run the FastAPI server pointing at your deployed Agent Engine resource:

```bash
cd frontend
export AGENT_ENGINE_RESOURCE_NAME="projects/<PROJECT_ID>/locations/us-east1/reasoningEngines/<REASONING_ENGINE_ID>"
export AGENT_DIRECTORY="app"
export PORT=8080

uv run python main.py
```

Open a browser and navigate to the port where the server is running (e.g., `8080`) to interact with FitCoach AI.

---

## 🚢 Deployment

### Deploy Agent to Agent Runtime / Cloud Run
```bash
agents-cli deploy --project <PROJECT_ID> --no-confirm-project
```

### Deploy Frontend to Cloud Run
```bash
gcloud run deploy fitcoach-frontend \
  --source ./frontend \
  --region us-east1 \
  --allow-unauthenticated \
  --set-env-vars AGENT_ENGINE_RESOURCE_NAME="projects/<PROJECT_ID>/locations/us-east1/reasoningEngines/<REASONING_ENGINE_ID>",AGENT_DIRECTORY="app"
```
