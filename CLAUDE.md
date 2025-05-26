# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

BioMCP (Biomedical Model Context Protocol) is an open-source toolkit that provides AI assistants with structured access to biomedical data sources including PubTator3/PubMed, ClinicalTrials.gov, and MyVariant.info.

## Development Commands

### Building and Installation

```bash
# Build the package
make build

# Install with all dependencies
make install

# Sync dependencies (development)
uv sync --all-extras
```

### Testing

```bash
# Run all tests
make test

# Run specific test file
uv run python -m pytest tests/path/to/test_file.py

# Run with coverage
uv run python -m pytest --cov=biomcp --cov-report=term-missing

# Run BDD tests only
uv run python -m pytest tests/bdd/

# Run unit tests only
uv run python -m pytest tests/tdd/
```

### Linting and Type Checking

```bash
# Format code
make format

# Run linter
make lint

# Type check
make typecheck
```

### Running the Application

```bash
# Start MCP server
biomcp run

# Use CLI
biomcp --help
biomcp articles search "cancer"
biomcp trials search "covid-19"
biomcp variants get "chr17:g.37880220T>A"
```

## High-Level Architecture

The codebase follows a modular architecture with three main data source modules:

1. **Articles Module** (`src/biomcp/articles/`)

   - Interfaces with PubTator3/PubMed API
   - Provides search, fetch, and autocomplete functionality

2. **Trials Module** (`src/biomcp/trials/`)

   - Interfaces with ClinicalTrials.gov API v2
   - Handles clinical trial search and retrieval

3. **Variants Module** (`src/biomcp/variants/`)
   - Interfaces with MyVariant.info API
   - Provides genetic variant annotations and search

### Key Components

- **HTTP Client** (`http_client.py`): Shared HTTP client with caching via diskcache
- **CLI** (`cli/`): Typer-based command-line interface
- **MCP Server** (`cli/server.py`): FastMCP server implementation
- **Workers** (`workers/`): Cloudflare Worker support for remote deployment
- **Resources** (`resources/`): Instruction files and prompts for AI assistants

### Remote Deployment

The project supports deployment as a Cloudflare Worker with OAuth authentication:

- Worker entry points in `workers/worker_entry.js` and `worker_entry_stytch.js`
- Configuration via `wrangler.toml`
- OAuth integration through Stytch

## Testing Strategy

- **Unit Tests** (`tests/tdd/`): Test individual components and functions
- **BDD Tests** (`tests/bdd/`): Behavior-driven tests using pytest-bdd with Gherkin features
- All API responses are mocked using pre-recorded JSON/text fixtures in `tests/data/`

## Important Notes

- Python 3.10+ required
- Uses `uv` for dependency management (not pip)
- HTTP requests are cached to disk to avoid rate limiting
- The project can run as both a local CLI tool and remote MCP server
- When modifying API integrations, check the corresponding test fixtures
