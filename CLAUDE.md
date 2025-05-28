# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a LangGraph tools agent for Open Agent Platform (OAP) that provides:
- MCP server integration for accessing external tools
- LangConnect RAG capabilities for document retrieval
- Custom authentication through Supabase

The agent is built using LangGraph and is designed to be used with Open Agent Platform, a citizen developer platform for building, testing, and using agents.

## Environment Setup

The project requires Python 3.11 or later (but less than 3.13) and uses `uv` for package management.

1. Create and activate a virtual environment:
```bash
uv venv
source .venv/bin/activate
```

2. Install dependencies:
```bash
uv run pip install -e .
```

3. Set up environment variables:
```bash
cp .env.example .env
```

## Required Environment Variables

The following environment variables are required:
- `ANTHROPIC_API_KEY` or `OPENAI_API_KEY`: At least one model API key
- `SUPABASE_URL`: URL for your Supabase instance
- `SUPABASE_KEY`: Supabase service role key

Optional environment variables:
- `LANGCHAIN_PROJECT`: Project name for LangSmith tracing
- `LANGCHAIN_API_KEY`: API key for LangSmith tracing
- `LANGCHAIN_TRACING_V2`: Enable LangChain tracing

## Development Commands

### Running the Server

```bash
uv run langgraph dev --no-browser
```

This starts the LangGraph server on http://localhost:2024

### Code Quality

Formatting code:
```bash
make format
```

Linting code:
```bash
make lint
```

## Architecture

The project consists of three main components:

1. **Agent**: `tools_agent/agent.py`
   - Defines the LangGraph agent configuration
   - Creates a ReAct agent with configured tools
   - Handles MCP tool integration
   - Provides RAG tool integration

2. **Authentication**: `tools_agent/security/auth.py`
   - Custom authentication handler using Supabase
   - Enforces user-based access control
   - JWT token verification
   - Owner-based permissions for threads and assistants

3. **Utilities**:
   - `tools_agent/utils/tools.py`: Creates and wraps MCP and RAG tools
   - `tools_agent/utils/token.py`: Handles token management and authentication with MCP servers

## Integration with Open Agent Platform

The agent is configured with specific UI elements for Open Agent Platform. The `GraphConfigPydantic` class in `agent.py` defines the configuration schema with UI metadata that OAP uses to render forms and options.

Key configuration elements:
- Model selection (Claude 3.7/3.5 Sonnet, GPT-4o/4o mini/4.1)
- Temperature control
- Maximum token count
- System prompt customization
- MCP server configuration
- RAG collection configuration

## Key Design Patterns

1. **Tool Registration**: The agent dynamically registers tools based on configuration
   - RAG tools are created based on collection IDs
   - MCP tools are fetched and wrapped with authentication handlers

2. **Token Management**: The system uses token exchange for MCP authentication
   - Supabase tokens are exchanged for MCP access tokens
   - Tokens are stored in the LangGraph store with expiration handling

3. **Authentication Flow**: 
   - JWT verification with Supabase
   - Owner-based access control for threads and assistants
   - Store namespace management for user data isolation