# Shotgrid MCP Server Tutorial

This tutorial demonstrates how to host an Autodesk Shotgrid MCP (Model Context Protocol) server on Amazon Bedrock AgentCore Runtime with AWS IAM authentication.

## Overview

The Shotgrid MCP server provides comprehensive tools for interacting with Autodesk Shotgrid's production management platform through AI agents. It wraps the Shotgrid Python API into MCP tools that can be deployed on AgentCore Runtime.

## Prerequisites

- Python 3.10+
- AWS credentials configured
- Amazon Bedrock AgentCore SDK
- MCP (Model Context Protocol) library
- Running Docker daemon
- Autodesk Shotgrid account with API access
- Shotgrid Python API (shotgun_api3)

## Environment Variables

The server requires the following environment variables:

```bash
export SHOTGRID_URL="https://your-studio.shotgunstudio.com"
export SHOTGRID_SCRIPT_NAME="your_script_name"
export SHOTGRID_API_KEY="your_api_key"
```

## Available Tools

### Query Tools
- `find_projects` - Find projects in Shotgrid
- `find_shots` - Find shots with optional project filtering
- `find_assets` - Find assets with optional project filtering
- `find_tasks` - Find tasks with optional entity filtering
- `find_versions` - Find versions with optional project filtering
- `find_users` - Find users in Shotgrid
- `find_notes` - Find notes with optional entity filtering

### Create Tools
- `create_shot` - Create a new shot in a project
- `create_asset` - Create a new asset in a project
- `create_task` - Create a new task for an entity
- `create_note` - Create a new note linked to entities

### Update Tools
- `update_entity` - Update any entity field
- `update_task_status` - Update a task's status

### Utility Tools
- `get_entity_by_id` - Get a single entity by ID
- `search_entities` - Search for entities by text
- `get_server_info` - Get Shotgrid server information

## Files

- `mcp_server.py` - Main MCP server implementation with 20+ Shotgrid tools
- `requirements.txt` - Python dependencies
- `hosting_mcp_server_iam_auth.ipynb` - Jupyter notebook tutorial for deployment
- `README.md` - This file

## Deployment to AgentCore Runtime

Follow the Jupyter notebook `hosting_mcp_server_iam_auth.ipynb` for step-by-step instructions on:

1. Configuring the AgentCore Runtime deployment
2. Building and pushing the Docker container
3. Deploying to AWS
4. Testing the deployed server with IAM authentication
5. Invoking Shotgrid tools remotely

## Architecture

The MCP server:
- Uses FastMCP with `stateless_http=True` for AgentCore compatibility
- Connects to Shotgrid using the official Python API
- Provides AWS IAM authentication for secure access
- Runs on port 8000 at the `/mcp` endpoint
- Supports stateless operation for scalability

## Example Usage

Once deployed, you can use the Shotgrid MCP server to:

```python
# Find all active projects
projects = await session.call_tool(
    name="find_projects",
    arguments={"filters": [["sg_status", "is", "Active"]]}
)

# Create a new shot
shot = await session.call_tool(
    name="create_shot",
    arguments={
        "project_id": 123,
        "code": "SH010",
        "description": "Opening shot"
    }
)

# Update task status
result = await session.call_tool(
    name="update_task_status",
    arguments={"task_id": 456, "status": "ip"}
)
```

## Notes

- All tools support pagination and filtering
- Error handling is built into each tool
- The server maintains a single Shotgrid connection that's reused across requests
