# voice-system-design

Single source of truth for the voice analysis system: architecture, data schemas, API specifications, and integration documents.

## Architecture Overview

The voice analysis system is designed around a backend-centered data flow: the DAW plugin captures voice-related session data and sends it to the backend server, the backend validates and stores it in the database, and the dashboard retrieves stored data through the backend API for review.

```text
DAW Plugin -> Backend Server/API -> Database
                             ^
                             |
                      Web Dashboard
```

### DAW Plugin

- Captures practice-session metadata from the user's recording workflow.
- Extracts or exports analysis data such as pitch, timing, and session markers.
- Sends structured session data to the backend API.

### Backend API

- Receives voice-session data from the DAW plugin.
- Validates incoming payloads before storing them.
- Provides API endpoints for the dashboard to retrieve sessions, analysis results, and practice notes.

### Database

- Stores voice sessions, pitch analysis results, and related practice notes.
- Keeps historical records so progress can be reviewed over time.
- Acts as the shared source of data between the backend and dashboard.

### Web Dashboard

- Displays recorded practice sessions and analysis summaries.
- Visualizes pitch trends, timing accuracy, and progress by song.
- Retrieves stored data through the backend API instead of accessing the database directly.
- Helps the user review practice history and identify improvement areas.
