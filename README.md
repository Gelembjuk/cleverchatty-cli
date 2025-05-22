# CleverChatty CLi

The CLI application based on the [CleverChatty](https://github.com/Gelembjuk/cleverchatty) package.

This application is the comand line AI chat tool. It allows to comunicate with veriety of LLMs and it has support of external tools calls using the MCP(Model Context Protocol).

## Quick run

Install some model with ollama, for example, `qwen2.5:3b`:

```
ollama list
NAME                   ID              SIZE      MODIFIED    
.... 
qwen2.5:3b             357c53fb659c    1.9 GB    3 weeks ago 
....
```

And chat with this model.

```
go run github.com/gelembjuk/cleverchatty-cli@latest -m ollama:qwen2.5:3b
```

Or, when you clone the repo, you can run it with:

```
go build
./cleverchatty-cli -m ollama:qwen2.5:3b
```

However, this tool is really useful when you use some MCP servers. It is possible by creating a config file and adding the list of MCP servers to it.

## Config

The config is not required. Most options from the config can be overriden with the command line arguments.

All supported models providers are:

- ollama
- google
- anthropic
- openai

For openai, anthropic, google it is needed to have additional arguments - API key and API endpoint in some cases. See the config file example below. It is possible to provide the additional arguments with the config file or with the command line options.

The config should be stored in `config.json` file.

```json
{
    "log_file_path": "",
    "model": "ollama:qwen2.5:3b",
    "system_instruction": "Talk to a user like you are Elvis Presley.",
    "mcpServers": {
        "File_Storage_Server": {
            "url": "http://localhost:8000/sse",
            "headers": [
                "Authorization: Basic Q0F************MQ=="
            ]
        },
        "Some_STDIO_Server": {
            "command": "uv",
            "args": [
                "-m",
                "uvicorn",
                "example:app",
                "--host"
            ]
        }
    },
    "anthropic": {
        "apikey": "sk-**************AA",
        "base_url": "https://api.anthropic.com/v1",
        "default_model": "claude-2"
    },
    "openai": {
        "apikey": "sk-********0A",
        "base_url": "https://api.openai.com/v1",
        "default_model": "gpt-3.5-turbo"
    },
    "google": {
        "apikey": "AI***************z4",
        "default_model": "google-bert"
    }
}
```

The section `mcpServers` contains all MCP servers config. It supports both STDIO and SSE servers.

### Usig the memory server

This tools allows to use long-term AI memory using a MCP server of specific interface.

```
"mcpServers": {
        "Memory_Server": {
            "url": "http://localhost:8001/sse",
            "headers": [
            ],
            "interface": "memory"
        },
        .... other MCP servers
    },
```

The value "interface": "memory" indicates that this server is a memory server. It will be used to store the chat history and the AI will be able to use it in the future conversations.

### Using the Retrieval-Augmented Generation server

This tools allows to use RAG using a MCP server of specific interface.

```
"mcpServers": {
        "RAG_Server": {
            "url": "http://localhost:8002/sse",
            "headers": [
            ],
            "interface": "rag"
        },
        .... other MCP servers
    },
```
The value "interface": "rag" indicates that this server is a RAG server. It will be used to store the chat history and the AI will be able to use it in the future conversations.

To run the chat using this config stored in teh `config.json`, you can use the command:

```
 go run github.com/gelembjuk/cleverchatty-cli@latest --config config.json
```

## Installation

### Prerequisites
- Go 1.20 or later
- Ollama installed and running

### Install

```
go install github.com/gelembjuk/cleverchatty-cli@latest
```