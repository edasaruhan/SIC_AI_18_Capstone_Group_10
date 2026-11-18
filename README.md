# BeforeScroll

BeforeScroll is a capstone project exploring an early-warning approach to potentially excessive app use. It generates **synthetic app-session data**, engineers time-aware features, trains a risk classifier, and serves an interactive dashboard with a Flask API.

> **Prototype notice:** This project is an educational MVP. It uses simulated data, not real device activity or personal user data. Its predictions are not clinical advice and should not be treated as a diagnosis.

## What it does

- Simulates app sessions for configurable users and days, including app category, time of day, notifications, session duration, and whether responsibilities have been completed.
- Labels potentially risky sessions using responsibility-aware rules.
- Builds features from past sessions available before a prediction point, avoiding the use of the current session's future information.
- Trains and evaluates Logistic Regression and HistGradientBoosting models using a chronological train/validation/test split.
- Runs a Flask dashboard/API that presents a demo user's risk estimate, usage summaries, task status, and suggestions.
- Supports a horizon sweep to compare prediction lead times of 1, 5, 10, and 30 minutes.

## Requirements

- Python 3.11 or later
- Dependencies listed in `requirements.txt`

## Run the dashboard locally

```bash
git clone https://github.com/edasaruhan/SIC_AI_18_Capstone_Group_10.git
cd SIC_AI_18_Capstone_Group_10
python -m venv .venv
```

Activate the virtual environment:

```bash
# macOS / Linux
source .venv/bin/activate

# Windows PowerShell
.venv\Scripts\Activate.ps1
```

Install dependencies and start the application:

```bash
pip install -r requirements.txt
python app.py
```

Open [http://localhost:8000](http://localhost:8000). On startup, the app generates its synthetic dataset and trains the model, so the first launch may take a few seconds.

## Run with Docker

```bash
docker compose up --build
```

Then visit [http://localhost:8000](http://localhost:8000). To stop the service, press `Ctrl+C` or run `docker compose down` in another terminal.

## Run the simulation and model evaluation

Run the command-line pipeline to generate session data, build features, train/evaluate the models, and print feature importance:

```bash
python main.py
```

By default, generated files are written to `output/`. To also evaluate multiple prediction horizons:

```bash
python main.py --sweep
```

Available options:

| Option | Default | Description |
| --- | ---: | --- |
| `--users` | `40` | Number of simulated users |
| `--days` | `28` | Number of simulated days |
| `--horizon` | `10` | Prediction horizon in minutes |
| `--seed` | `42` | Random seed |
| `--out` | `output` | Output directory |
| `--sweep` | off | Evaluate 1-, 5-, 10-, and 30-minute horizons |

For example:

```bash
python main.py --users 100 --days 30 --horizon 5 --seed 7 --out output --sweep
```

The pipeline writes `sessions.csv` and `features_h{horizon}.csv`; when `--sweep` is enabled it also writes `horizon_sweep.csv`.

## API

The Flask application serves the dashboard at `/` and provides these JSON endpoints:

| Method | Endpoint | Purpose |
| --- | --- | --- |
| `GET` | `/api/state` | Get current demo state and risk information |
| `POST` | `/api/tasks` | Update a task's completion state; JSON body: `{"i": 0, "done": true}` |
| `POST` | `/api/checkin` | Record a check-in; JSON body: `{"ok": true}` |
| `POST` | `/api/ask` | Ask a supported dashboard question; JSON body: `{"q": "..."}` |

## Project structure

```text
.
├── app.py                 # Flask dashboard server and API routes
├── config.py              # Simulation and risk-rule configuration
├── simulate.py            # Synthetic session data generation and labeling
├── features.py            # Time-aware feature engineering
├── model.py               # Model training, evaluation, and horizon sweep
├── main.py                # Command-line simulation/training pipeline
├── beforescroll/
│   └── service.py         # Demo engine used by the dashboard
├── web/
│   └── index.html         # Dashboard UI
├── requirements.txt
├── Dockerfile
└── docker-compose.yml
```

## Technology

Python, Flask, NumPy, pandas, and scikit-learn.

## License

No license is currently specified in this repository. Unless a license is added, all rights remain with the copyright holder.
