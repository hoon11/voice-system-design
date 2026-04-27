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

## Data Flow

The system separates the write flow from the read flow. The DAW plugin is responsible for producing voice-session data, while the web dashboard only reads processed data through the backend API.

### Write Flow

1. The user records or reviews a vocal practice session inside the DAW.
2. The DAW plugin captures session metadata, pitch data, timing data, and optional markers.
3. The DAW plugin sends the structured payload to the backend API.
4. The backend validates the payload format and required fields.
5. The backend stores the session, analysis results, and related metadata in the database.

### Read Flow

1. The user opens the web dashboard.
2. The dashboard requests session summaries or detailed analysis data from the backend API.
3. The backend reads the requested records from the database.
4. The backend returns normalized response data to the dashboard.
5. The dashboard renders charts, session history, and practice review information.

## Data Schema

The initial schema focuses on three core records: the recorded voice session, the pitch analysis generated from that session, and the user's practice notes.

### VoiceSession

Represents one vocal practice recording session.

- `id`: Unique session identifier.
- `songTitle`: Title of the song practiced in the session.
- `recordedAt`: Date and time when the vocal session was recorded.
- `durationSec`: Recording duration in seconds.
- `sourcePluginVersion`: DAW plugin version that generated the session data.

### PitchAnalysis

Represents analysis results produced from a voice session.

- `id`: Unique analysis identifier.
- `sessionId`: VoiceSession identifier connected to this analysis.
- `averagePitch`: Average detected pitch for the session.
- `pitchRange`: Lowest and highest detected pitch values.
- `timingAccuracy`: Timing accuracy score or summary.
- `analysisSummary`: Human-readable summary of the analysis result.

### PracticeNote

Represents notes written while reviewing a practice session.

- `id`: Unique note identifier.
- `sessionId`: VoiceSession identifier connected to this note.
- `note`: Free-form practice memo.
- `createdAt`: Date and time when the note was created.

### Version Compatibility

`sourcePluginVersion` allows the backend to handle changes between DAW plugin versions. If an older plugin sends a legacy payload, the backend can apply a compatible parser, migrate the data shape, or reject unsupported versions with a clear error response.
