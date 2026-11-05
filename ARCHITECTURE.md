# Architecture

This document describes how data moves through our Discord music bot, using a System Context Diagram and Data Flow Diagram.


**Notation**

| Symbol | Meaning |
| --- | --- |
| Gray rectangle | External entity |
| Yellow circle | Process |
| Blue cylinder | Data store |
| Arrow | Data flow |

## System Context Diagram

The whole bot is shown as a single process, together with the external systems it talks to.

![Level 0 Context Diagram](docs/images/Discord%20Music%20Bot%20-%20System%20Content%20Diagram.png)

## Data Flow Diagram

![Level 1 Data Flow Diagram](docs/images/level1-dfd.png)
### Processes

| ID | Process | Command | External service |
| --- | --- | --- | --- |
| 1.0 | AI Song Recommendation by mood | AI chat | Google Gemini API |
| 2.0 | Similar Song Recommendation | `/similar` | Google Gemini API |
| 3.0 | Manage Favorites | `/saveFavorite`, `/viewFavorites` | iTunes API, D1 Favorites DB |
| 4.0 | Song Preview Card | `/song` | iTunes API |
| 5.0 | Voice Channel Preview | `/play` | iTunes API |


## Network Diagram

This diagram shows where the bot runs and what it connects to over the network.

```mermaid
flowchart LR
  %% ---------- PEOPLE ----------
  %% Users never talk to the bot directly; they only use the Discord app.
  USER["Discord users"]

  %% ---------- WHERE THE BOT RUNS ----------
  %% Codespaces builds a container (from .devcontainer/) on Microsoft Azure.
  %% The bot only exists while someone is running it in a Codespace.
  subgraph GH["GitHub Codespaces (Microsoft Azure)"]
    subgraph C["Container"]
      BOT["Bot (Node.js)"]
    end
  end

  %% ---------- EXTERNAL SERVICES ----------
  %% Each subgraph is a separate company's network.
  subgraph DC["Discord"]
    GW["Gateway"]
    API["REST API"]
    VOICE["Voice server"]
  end

  subgraph APPLE["Apple"]
    ITUNES["iTunes Search API"]
    PREVIEW["Audio previews"]
  end

  subgraph GOOGLE["Google"]
    GEMINI["Gemini API"]
  end

  %% ---------- CONNECTIONS ----------
  %% Users reach Discord through the Discord app.
  USER -- "Discord app" --> DC

  %% Discord: one open WebSocket to hear messages, HTTPS to reply, UDP for voice.
  BOT -- "WebSocket: receives messages" --> GW
  BOT -- "HTTPS: sends replies" --> API
  BOT -- "UDP: streams /play audio" --> VOICE

  %% Apple: /song and /play search iTunes; /play also downloads the preview file.
  BOT -- "HTTPS: song search" --> ITUNES
  BOT -- "HTTPS: downloads preview" --> PREVIEW

  %% Google: AI commands send the question to Gemini.
  BOT -- "HTTPS: AI questions" --> GEMINI
```

### How to read this diagram

- **Boxes** are services. **Outlined areas** are separate networks owned by different companies.
- **Arrows** point from whoever starts the connection. All of the bot's connections start at the bot, so nothing on the internet connects into the Codespace.

### Connection types

| Type | Used for | How it works |
|------|----------|--------------|
| WebSocket | Receiving messages from Discord | One connection the bot opens at startup and keeps open, so Discord can push new messages anytime |
| HTTPS | Replies, song search, audio download, AI | The bot sends a request and waits for one response |
| UDP | Streaming `/play` audio | A fast live stream that does not wait to confirm every packet arrived |
