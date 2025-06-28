# 💧 Water Forecast

Water Forecast is a backend application built with **FastAPI**, responsible for serving AI-based water consumption forecasts, managing database interactions, and exposing RESTful APIs to frontend clients and AI modules like [water-forecast-ai].

## 🚀 Features

- **FastAPI server** with clean and modular architecture.
- **Database integration** using SQLAlchemy ORM with PostgreSQL.
- **Migrations** powered by Alembic for schema version control.
- **Redis support** for caching and fast access to AI-generated forecasts.
- **Structured configuration** with Pydantic-based settings.
- **Code quality** enforced with `ruff` and `mypy`.
- **Testing and coverage** with `pytest`.
- **Environment and tooling management** via `uv` and `pyproject.toml`.

## 🧭 System Architecture

The diagram below illustrates the initial architecture design for the Water Forecast system, highlighting the interaction between the web client, FastAPI server, cache/database layers, and AI runner models.

```mermaid
flowchart LR
    %% Groups
    subgraph client ["Client"]
        web_client(["Web Client"])
    end

    subgraph server ["Server"]
        web_server(["Web Server"])
        database[(Database)]
        cache_db[(Cache Database)]
    end

    subgraph runner ["Runner"]
        runner_manager{{"Runner Manager"}}
        runner_model_a(("ML Model A"))
        runner_model_b(("ML Model B"))
        runner_model_n(("ML Model N"))
    end

    %% Client to server
    web_client -->|HTTP/HTTPS| web_server

    %% Server interactions
    web_server --> database
    web_server -.->|Write Cache| cache_db

    %% Manager polls from cache
    runner_manager -.->|Poll Cache| cache_db

    %% Manager triggers runners
    runner_manager --> runner_model_n
    runner_manager --> runner_model_b
    runner_manager --> runner_model_a

    %% Runners call server
    runner_model_a ==>|Call API| web_server
    runner_model_b ==>|Call API| web_server
    runner_model_n ==>|Call API| web_server

    %% Runners read/write cache
    runner_model_a -.-> cache_db
    runner_model_b -.-> cache_db
    runner_model_n -.-> cache_db

    %% Styling
    classDef dashed stroke:#666,stroke-dasharray: 5 5;
    classDef apiCall stroke:#1e90ff,stroke-width:2px;
    classDef runnerNode fill:#d0e4ff,stroke:#003c8f,stroke-width:2px,color:#003c8f;
    classDef cacheStyle fill:#d82c20,stroke:#a41e11,stroke-width:2px,color:#ffffff;
    classDef dbStyle fill:#336791,stroke:#1e4e79,stroke-width:2px,color:#ffffff;

    class cache_db cacheStyle;
    class database dbStyle;
    class web_server apiCall;
    class runner_model_a,runner_model_b,runner_model_n runnerNode;
    class web_server,runner_manager,database,cache_db dashed;
```

## ⚙️ Environment Setup

This project uses [uv] for Python environment and dependency management.

### Install uv

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### Create and activate virtual environment

```bash
uv venv .venv
source .venv/bin/activate
```

### Install development dependencies

```bash
uv pip install -e ".[dev]"
```

## 🔒 Dependency Locking

To generate a lockfile for reproducible environments:

```bash
pip-compile pyproject.toml --extra=dev --output-file=requirements.lock.txt
```

This ensures all dependencies (including transitive ones) are pinned and can be synced across environments with full reproducibility.

## 🧪 Running Tests

After activating the virtual environment:

```bash
pytest
```

With coverage report:

```bash
pytest --cov=water_forecast --cov-report=term-missing
```

## 🧹 Code Quality Checks

### Run Ruff (linter & formatter)

```bash
ruff check water_forecast
```

### Run Mypy (type checker)

```bash
mypy water_forecast
```

## 🌐 API Documentation

Once the server is running, access the interactive API docs at:

```
http://localhost:8000/docs
```

Or the alternative ReDoc view:

```
http://localhost:8000/redoc
```

---

This backend service is designed to work in tandem with [water-forecast-ai] and frontend clients for delivering accurate and real-time water consumption predictions.

[uv]: https://github.com/astral-sh/uv
[water-forecast-ai]: https://github.com/ramironunes/water-forecast-ai/
