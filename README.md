# MCP Course - arXiv Paper Chatbot

An AI chatbot that uses MiniMax's LLM API to search for and extract information about arXiv papers through function calling (tool definitions).

## Features

- **Search arXiv papers**: Search for academic papers on any topic
- **Extract paper information**: Retrieve detailed information about specific papers
- **Interactive CLI**: Chat with the bot in an interactive command-line interface
- **Tool execution**: The LLM can autonomously call tools to search for papers and retrieve information

## Prerequisites

- Python 3.10+
- MiniMax API key

## Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd mcp-course
```

2. Create and activate a virtual environment:
```bash
python -m venv venv
source venv/bin/activate  # On macOS/Linux
# or
venv\Scripts\activate  # On Windows
```

3. Install dependencies:
```bash
pip install -r requirements.txt
```

4. Configure environment variables:

Create a `.env` file in the project root with the following content:
```env
ANTHROPIC_BASE_URL=https://api.minimaxi.com/anthropic
ANTHROPIC_API_KEY=your-api-key-here
```

Replace `your-api-key-here` with your MiniMax API key.

## Usage

Run the chatbot:
```bash
python chatbot.py
```

### Example queries

- "Search for papers about machine learning"
- "Search for papers about large language models"
- "Tell me about paper 2310.12345"

Type `quit` to exit the chat.

## Project Structure

```
mcp-course/
├── chatbot.py          # Main chatbot implementation
├── .env                # Environment variables (API keys)
├── CLAUDE.md          # Claude Code instructions
├── papers/             # Directory for storing paper data
│   └── <topic>/
│       └── papers_info.json
├── venv/               # Python virtual environment
└── chatbot.log         # Chat logs
```

## Tools

The chatbot provides two tools:

1. **search_papers**: Search arXiv for papers on a topic
   - `topic`: The topic to search for
   - `max_results`: Maximum number of results (default: 5)

2. **extract_info**: Retrieve information about a specific paper
   - `paper_id`: The arXiv paper ID (e.g., "2310.12345")

## Architecture

- **chatbot.py**: Core implementation with:
  - `search_papers()` - Searches arXiv and saves paper info to JSON
  - `extract_info()` - Retrieves saved paper information
  - `process_query()` - Main query processing loop with tool execution
  - `chat_loop()` - Interactive CLI loop

- **Tool System**: Tools are defined using the Anthropic SDK's tool schema format, enabling the LLM to autonomously decide when to call tools based on user queries.

## License

MIT