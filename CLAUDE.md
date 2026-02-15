# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an MCP (Model Context Protocol) course project that implements an AI chatbot using MiniMax's LLM API. The chatbot can search for arXiv papers and extract information about them using function calling (tool definitions).

## Commands

### Running the chatbot
```bash
python chatbot.py
```

### Virtual environment
The project uses a Python virtual environment at `venv/`. Activate it with:
```bash
source venv/bin/activate
```

### Dependencies
Key dependencies (already in venv):
- `anthropic` - Anthropic SDK for API calls
- `arxiv` - arXiv paper search
- `python-dotenv` - Environment variable loading

## Architecture

### Main Components

**chatbot.py** - Core chatbot implementation with:
- `search_papers(topic, max_results)` - Searches arXiv and saves paper info to JSON
- `extract_info(paper_id)` - Retrieves saved paper information
- `process_query(query)` - Main query processing loop with tool execution
- `chat_loop()` - Interactive CLI loop

### Tool System
The chatbot defines two tools:
1. `search_papers` - Search arXiv for papers on a topic
2. `extract_info` - Get information about a specific paper

Tools are defined in the `tools` list and mapped to functions in `mapping_tool_function`.

### Data Flow
1. User enters a query in `chat_loop()`
2. `process_query()` sends the query to MiniMax API
3. If the model returns a tool call, execute the tool and append results to messages
4. Continue the loop until the model returns only text

### Important Implementation Notes

**Multi-turn Tool Call Message History**: When the model returns multiple content blocks (e.g., thinking + tool_use), the complete `response.content` must be added to message history, not individual blocks. This is required by MiniMax API to maintain the thinking chain continuity.

**API Configuration**: Uses MiniMax endpoint via Anthropic SDK:
- Base URL: `https://api.minimaxi.com/anthropic`
- Model: `MiniMax-M2.5`

### Data Storage
- Papers are saved to `papers/<topic>/papers_info.json`
- Logs are written to `chatbot.log`

## Environment Variables

Required in `.env`:
- `ANTHROPIC_BASE_URL` - API endpoint
- `ANTHROPIC_API_KEY` - API key for authentication
