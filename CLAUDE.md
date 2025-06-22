# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

GPT Researcher is an autonomous AI agent designed for comprehensive web and local research. It produces detailed, factual research reports with citations by coordinating multiple specialized agents (planner, executor, publisher). The project supports multiple deployment options and integrates with various LLM providers and search engines.

## Architecture

The project follows a multi-layered architecture:

- **Core Research Engine** (`gpt_researcher/`): Main research logic, agent coordination, and report generation
- **Backend Services** (`backend/`): FastAPI server, chat interface, websocket management, and memory systems  
- **Multi-Agent System** (`multi_agents/`): LangGraph-based coordination of specialized agents (researcher, writer, editor, reviewer, publisher)
- **Frontend Options**: Lightweight HTML/CSS/JS (`frontend/index.html`) and production NextJS app (`frontend/nextjs/`)
- **MCP Integration** (`gptr-mcp/`): Model Context Protocol server for AI assistant integration

## Development Commands

### Running the Application
```bash
# Start MCP Server (replaces old FastAPI backend)
uv run gptr-mcp/server.py

# Start FastAPI backend server (legacy)
python -m uvicorn main:app --reload

# Start FastAPI server with custom host/port (legacy)
python -m uvicorn backend.server.server:app --host=0.0.0.0 --port=8000

# Run directly via main.py (legacy)
python main.py

# Docker deployment (simplified - no longer includes gpt-researcher service)
docker-compose up --build
```

### Frontend Development
```bash
# NextJS frontend (production)
cd frontend/nextjs
npm run dev          # Development server
npm run build        # Production build
npm run test         # Run tests

# Static frontend served by FastAPI at localhost:8000
```

### Testing
```bash
# Python tests
python -m pytest                    # All tests
python -m pytest tests/            # Test directory
python -m pytest -v               # Verbose output

# Docker tests  
docker-compose run gpt-researcher-tests

# Specific test files for components
python tests/test-your-llm.py      # Test LLM integration
python tests/test-your-retriever.py # Test search engines
python tests/test_mcp.py           # Test MCP functionality
```

### Multi-Agent System
```bash
# Run multi-agent research workflow
cd multi_agents
python main.py

# LangGraph visualization and monitoring
langgraph dev    # Development server for LangGraph
```

## Key Configuration

### Environment Variables
- `OPENAI_API_KEY` / `ANTHROPIC_API_KEY`: LLM provider keys
- `TAVILY_API_KEY`: Default web search engine
- `DOC_PATH`: Local documents directory for research
- `RETRIEVER`: Search engine selection (tavily, google, bing, duckduckgo, etc.)
- `REPORT_TYPE`: Output format (research_report, detailed_report, etc.)

### MCP Integration
```bash
# Enable hybrid web + MCP research
export RETRIEVER=tavily,mcp

# MCP server configurations can be passed to GPTResearcher class
```

## Code Structure Patterns

### Research Workflow
1. **Agent Creation** (`gpt_researcher/agent.py`): Task-specific agent initialization
2. **Query Processing** (`gpt_researcher/actions/query_processing.py`): Research question generation  
3. **Information Retrieval** (`gpt_researcher/retrievers/`): Multi-source data gathering
4. **Content Scraping** (`gpt_researcher/scraper/`): Web content extraction
5. **Report Generation** (`gpt_researcher/actions/report_generation.py`): Final report compilation

### Multi-Agent Coordination
- **Orchestrator** (`multi_agents/agents/orchestrator.py`): Workflow coordination
- **Researcher** (`multi_agents/agents/researcher.py`): Information gathering
- **Writer** (`multi_agents/agents/writer.py`): Content creation
- **Editor/Reviewer/Reviser**: Quality control and refinement
- **Publisher** (`multi_agents/agents/publisher.py`): Final output generation

### Frontend Integration
- **WebSocket Communication**: Real-time research progress updates via `backend/server/websocket_manager.py`
- **API Endpoints**: RESTful interfaces in `backend/server/app.py`
- **Static Assets**: Served from `frontend/static/` for lightweight deployment

## Important Implementation Notes

- The project uses **async/await patterns** extensively for concurrent operations
- **LangChain/LangGraph** integration for agent orchestration and LLM interactions
- **FastAPI** backend with WebSocket support for real-time updates
- **Multiple output formats** supported (PDF, DOCX, Markdown) via `md2pdf`, `python-docx`
- **Configurable retrievers** allow switching between search engines and local documents
- **MCP protocol support** enables integration with AI assistants like Claude Desktop

## Testing Strategy

- Unit tests for individual components in `tests/`
- Integration tests for agent workflows
- LLM provider testing utilities (`test-your-llm.py`)
- Search engine validation (`test-your-retriever.py`)
- Document processing validation (`test-loaders.py`)

## Git Remote Configuration

This repository is configured with multiple remotes for development workflow:

- **origin**: `https://github.com/kicksent/gpt-researcher.git` (your fork for pushing changes)
- **upstream**: `https://github.com/assafelovic/gpt-researcher.git` (original repository for pulling updates)

### Common Git Workflows
```bash
# Push changes to your fork
git push origin branch-name

# Pull updates from original repository
git pull upstream main

# Create feature branch
git checkout -b feature/your-feature-name

# Push feature branch to fork for PR
git push origin feature/your-feature-name
```

## MCP Server Integration

The project now includes a dedicated MCP (Model Context Protocol) server that replaces the traditional FastAPI backend for AI assistant integration:

### Starting the MCP Server
```bash
# Start GPT-Researcher MCP Server (port 8000)
uv run gptr-mcp/server.py
```

### MCP Server Features
- **Streamable HTTP Transport**: Uses streamable-http protocol for real-time communication
- **AI Assistant Integration**: Direct integration with Claude Desktop and other AI assistants
- **Research Capabilities**: Full GPT-Researcher functionality via MCP protocol
- **Port Configuration**: Runs on port 8000 (http://0.0.0.0:8000/mcp)