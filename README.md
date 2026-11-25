# Multi-Agent Collective Intelligence Simulator

An executable research notebook for studying coordination and collective decision-making in a seeded population of heterogeneous agents. It includes a rule-based baseline and an optional LLM-assisted experiment that records recommendations separately for analysis.

The project is intended for controlled modeling experiments, not claims about machine consciousness or genuine altruism.

## What it includes

- Heterogeneous agent traits for altruism, conformity, trust, and risk aversion, plus state such as reputation, resources, and commitment
- A Watts-Strogatz social network built with NetworkX
- A deterministic decision model for cooperation, sacrifice, and defection
- Social-pressure and peer-influence effects
- Multi-round simulation and parameter sweeps across random seeds
- Plotly and NetworkX visualizations
- Pandas and Polars event analytics
- Optional LangGraph orchestration and constrained OpenAI Responses API calls
- Optional PostgreSQL persistence and reproducible CSV exports

Agent “sacrifice” is a modeled, irreversible action. It is not evidence of emotions, consciousness, or real-world agency.

## Requirements

- Python 3.10+
- A virtual environment
- JupyterLab
- Internet access only if installing packages or using the optional OpenAI API

The Python dependencies are listed in `requirements.txt`.

## Setup

### Linux or macOS

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
jupyter lab
```

### Windows

```powershell
py -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
Copy-Item .env.example .env
jupyter lab
```

Open `multi_agent_collective_intelligence.ipynb` and run the cells in order. The first setup cell installs the notebook's simulation dependencies; `requirements.txt` also installs JupyterLab and the notebook kernel.

The supplied `.env.example` sets `USE_LLM=True`. If you want a rule-based-only run, change it to `USE_LLM=False` in `.env` before running the notebook. To use LLM mode, configure a valid `OPENAI_API_KEY` first.

## Notebook workflow

The notebook covers:

1. Environment and configuration
2. Research model and agent population
3. Social-network construction and community detection
4. Rule-based decision engine and simulation
5. Analytics and parameter sweeps
6. GPT-6 Luna recommendations
7. LangGraph decision workflow
8. Opt-in LLM-assisted experiment
9. Optional PostgreSQL persistence
10. Polars analytics and CSV export
11. Research experiment design

The baseline configuration uses 100 agents, up to 12 rounds, a mission threshold of 20 sacrifices, and seed 42. The simulation can stop early when the threshold is reached.

## LLM mode

LLM mode is controlled by environment variables loaded from `.env`. To opt in, configure:

```dotenv
OPENAI_API_KEY=your_api_key_here
OPENAI_MODEL=gpt-6-luna
USE_LLM=True
MAX_LLM_CALLS=8
MAX_LLM_OUTPUT_TOKENS=1024
```

The notebook defaults to `USE_LLM=False` when the variable is unset. The provided `.env.example` overrides that default with `USE_LLM=True`, so set it to `False` for a rule-based-only run. The defaults for the other settings are 8 calls and 1024 output tokens.

Keep `USE_LLM=False` for deterministic, offline experiments. Set it to `True` only when you are ready to make API calls and have configured the API key.

When enabled:

- The LLM receives a constrained input and structured output schema
- It may recommend `cooperate`, `sacrifice`, or `defect`
- The LangGraph workflow validates recommendations and falls back to the deterministic engine for invalid output
- The notebook exposes `MAX_LLM_CALLS` and `MAX_LLM_OUTPUT_TOKENS` settings
- The model does not directly mutate agent state
- LLM results are kept in a separate `llm_results` DataFrame

No API key is written into the notebook. Never commit `.env` or API credentials.

## PostgreSQL persistence

Create a PostgreSQL database and set `DATABASE_URL` in `.env`. The example format is:

```dotenv
DATABASE_URL=postgresql+psycopg://postgres:password@localhost:5432/collective_sim
```

The notebook can create a `simulation_events` table and persist event fields such as round, agent ID, action, probability, resources, and commitment state.

Call:

```python
persist_events(events_df)
```

after the simulation runs. If `DATABASE_URL` is absent, persistence skips. The function defaults to `recreate=False`; `recreate=True` drops and recreates the table, so use it only when you intend to discard existing table contents. The persistence cell's example uses `recreate=True` for an initial setup.

Table names must start with a letter or underscore and contain only letters, numbers, or underscores.

## Generated outputs

The export cell writes these files into `outputs/`:

- `outputs/simulation_events.csv`
- `outputs/parameter_sweep.csv`
- `outputs/event_summary.csv` (events grouped by round and event type)

Add database migrations, access controls, and an experiment-tracking system before using the notebook for a production or published study.

## Research caveats

- Results depend on the fixed utility function and toy social-network model
- One run is not sufficient to estimate uncertainty
- Compare multiple seeds and report confidence intervals and effect sizes
- Preserve prompts, model IDs, timestamps, and raw outputs when comparing LLM policies
- Treat generated rationales as constrained model text, not as internal reasoning evidence
