# claude-to-sqlite

[![PyPI](https://img.shields.io/pypi/v/claude-to-sqlite.svg)](https://pypi.org/project/claude-to-sqlite/)
[![Changelog](https://img.shields.io/github/v/release/simonw/claude-to-sqlite?include_prereleases&label=changelog)](https://github.com/simonw/claude-to-sqlite/releases)
[![Tests](https://github.com/simonw/claude-to-sqlite/actions/workflows/test.yml/badge.svg)](https://github.com/simonw/claude-to-sqlite/actions/workflows/test.yml)
[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](https://github.com/simonw/claude-to-sqlite/blob/master/LICENSE)

Convert a [Claude.ai](https://claude.ai/) export to SQLite

## Installation

Install this tool using `pip`:
```bash
pip install claude-to-sqlite
```
## Usage

Start by [exporting your Claude data](https://support.anthropic.com/en/articles/9450526-how-can-i-export-my-claude-ai-data). You will be emailed a link to a zip file (though it may be missing the `.zip` extension).

Run the command like this:

```bash
claude-to-sqlite claude-export.zip claude.db
```
Now `claude.db` will be a SQLite database containing the `memories`, `conversations`, `messages`, `attachments`, and `artifacts` from your Claude export.

You can explore that using [Datasette](https://datasette.io/):

```bash
datasette claude.db
```
## Database schema

The database contains the following tables:

### memories

Claude's accumulated memory/context about you.

| Column | Type | Description |
|--------|------|-------------|
| account_uuid | TEXT PRIMARY KEY | Account UUID |
| conversations_memory | TEXT | Claude's memory about your preferences, context, etc. |

### conversations

Conversation metadata.

| Column | Type | Description |
|--------|------|-------------|
| uuid | TEXT PRIMARY KEY | Conversation UUID |
| name | TEXT | Conversation title |
| summary | TEXT | Conversation summary |
| created_at | TEXT | ISO timestamp |
| updated_at | TEXT | ISO timestamp |
| account_id | TEXT | Account UUID |

### messages

Individual chat messages.

| Column | Type | Description |
|--------|------|-------------|
| uuid | TEXT PRIMARY KEY | Message UUID |
| text | TEXT | Message content |
| sender | TEXT | Either 'human' or 'assistant' |
| created_at | TEXT | ISO timestamp |
| updated_at | TEXT | ISO timestamp |
| conversation_id | TEXT | Links to conversations.uuid |

### attachments

Files attached to messages with extracted content.

| Column | Type | Description |
|--------|------|-------------|
| message_uuid | TEXT | Links to messages.uuid |
| file_name | TEXT | Original filename |
| file_size | INTEGER | Size in bytes |
| file_type | TEXT | MIME type |
| extracted_content | TEXT | Text extracted from the file |

### artifacts

Code blocks and other artifacts from assistant messages.

| Column | Type | Description |
|--------|------|-------------|
| id | TEXT PRIMARY KEY | Artifact ID with version |
| artifact | TEXT | Base artifact identifier |
| identifier | TEXT | Artifact identifier from tag |
| version | INTEGER | Version number |
| type | TEXT | Artifact type |
| language | TEXT | Programming language |
| title | TEXT | Artifact title |
| content | TEXT | Artifact content |
| thinking | TEXT | Associated thinking content |
| conversation_id | TEXT | Links to conversations.uuid |
| message_id | TEXT | Links to messages.uuid |

## Development

To contribute to this tool, first checkout the code. Then create a new virtual environment:
```bash
cd claude-to-sqlite
python -m venv venv
source venv/bin/activate
```
Now install the dependencies and test dependencies:
```bash
pip install -e '.[test]'
```
To run the tests:
```bash
python -m pytest
```
