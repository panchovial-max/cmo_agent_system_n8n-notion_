## CMO Agent Management System - Notion Sync

This repository contains an n8n workflow that synchronizes CMO Agent performance, relationships, and integration health with Notion databases, and raises alerts as Notion tasks when needed.

### Files

- `workflows/cmo_agent_sync_workflow.json`: Importable n8n workflow definition.
- `workflows/cmo_content_generation_workflow.json`: Importable workflow for Clients → Briefs → OpenAI Concepts.

### Prerequisites

- An n8n instance (self-hosted or n8n Cloud)
- A Notion internal integration with access to your target databases
- The following Notion databases ready and shared with your Notion integration:
  - CMO Agents
  - Agent Relationships
  - n8n Integrations (log)
  - Marketing Tasks
  - Clients (Title: Client_Name; Select: Industry)
  - Briefs (Title: Brief_Title; Relation: Client; Text: Objectives, Target_Audience)
  - Concepts (Title: Concept_Title; Text: Tagline, Key_Message, Channels; Relation: Brief)

### Import the Workflow in n8n

1. Open n8n → Workflows → Import from File.
2. Select `workflows/cmo_agent_sync_workflow.json` or `workflows/cmo_content_generation_workflow.json`.
3. Save the workflow.

### Configure Workflow Settings

In the imported workflow, open Settings and set the following:

- `timezone`: `America/New_York` (or your preferred timezone)
- `cmoAgentDbId`: Notion Database ID for CMO Agents
- `agentRelationshipsDbId`: Notion Database ID for Agent Relationships
- `n8nIntegrationsDbId`: Notion Database ID for n8n Integrations
- `marketingTasksDbId`: Notion Database ID for Marketing Tasks
For the content generation workflow:
- `clientsDbId`: Notion Database ID for Clients
- `briefsDbId`: Notion Database ID for Briefs
- `conceptsDbId`: Notion Database ID for Concepts

How to get a Notion Database ID: open the database as a full page in Notion, copy the URL, and use the 32-char ID segment.

### Credentials

Create Notion credentials in n8n:

- Name: `Notion CMO Integration` (or update the workflow to match your credential name)

Ensure your credential has access to all databases above.

Create OpenAI credentials in n8n (for the content workflow):

- Name: `OpenAI CMO` (or update the workflow to match)
- Model used: `gpt-4o-mini` (adjust if needed)

### Schedule and Behavior

The workflow runs on a schedule at 09:00, 14:30, and 18:00 (workflow timezone). It:

- Reads agents, relationships, integrations, and active marketing tasks
- Computes agent performance metrics and hierarchy counts
- Updates agent records in batches of 5
- Logs a sync run in the `n8n Integrations` database
- Creates a high-priority `Marketing Task` if any integration needs attention

You can run it manually for testing; results are logged in Notion.

For the content generation workflow:

- Manual trigger builds a prompt from a Brief and generates 5 Concepts via OpenAI
- Concepts are parsed and inserted into the `Concepts` database, related back to the Brief

### Notes

- If your property names differ, adjust them in the Notion nodes or in the Code node.
- The workflow references an error workflow via `error-handler-workflow-id`. Replace with your own or remove if unused.
 - The content workflow includes example create nodes (Client/Brief). Replace with your own data flow or feed real items in via webhook/trigger.


