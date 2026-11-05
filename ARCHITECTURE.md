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

## Use Case Diagram

This use case diagram depicts all the possible commands a discord user can input. It shows which commands are connected to an external service that returns requested data and which commands are handled entirely within the discord system. 

![Level 2 Use Case Diagram](docs/images/docs/images/Music Bot - Use Case Diagram.jpg)

| Color | Meaning |
| --- | --- |
| Blue | Internal Command |
| Yellow | Externally Supported Command |
| Gray | External Service |
| Purple | User |
| Light Yellow | Internal Store | 
