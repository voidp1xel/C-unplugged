<div align="center">

# 🎵 C-Unplugged — CLI Music Library & Playlist Engine

**A zero-dependency, terminal-based music library and playlist management engine engineered in C.**

![Language](https://img.shields.io/badge/Language-C99%2FC11-00599C?style=for-the-badge&logo=c&logoColor=white)
![Build](https://img.shields.io/badge/Build-GNU_Make-427819?style=for-the-badge&logo=gnu&logoColor=white)
![Data Structures](https://img.shields.io/badge/Data_Structures-Circular_DLL_%26_SLL-ff69b4?style=for-the-badge)
![Platform](https://img.shields.io/badge/Platform-POSIX_%7C_macOS_%7C_Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)

</div>

---

## 📌 Executive Summary

**C-Unplugged** is a modular command-line music library, album organizer, and interactive playlist engine written from scratch in **C**. Designed to demonstrate low-level systems programming, manual heap lifecycle management, custom file serialization, and pointer-intensive data structures, it replicates core desktop music player workflows—including continuous looped playback, album-to-queue batch ingestion, and persistent session auditing—inside an ANSI-styled terminal interface.

---

## 🌟 Key Engineering Features

- 🎧 **Real-Time "Now Playing" HUD**: Active playback state is continuously tracked and rendered in an ANSI-colored `▶ NOW PLAYING` status banner at the top of the interactive event loop.
- 🔁 **Unbounded Looped Playback (Circular Doubly Linked List)**: The active playback queue is backed by a **Circular Doubly Linked List**, enabling $O(1)$ bidirectional track traversal (`Play Next` / `Play Previous`) with seamless wrap-around between tail and head nodes.
- ⚡ **Indexed O(N) Catalog Lookup & Batch Enqueueing**: Supports direct track selection via auto-indexed serial IDs as well as bulk album-to-playlist injection without string-matching overhead.
- 💾 **Custom Text & Binary-Style Persistence**: Automatically deserializes the master song repository (`songs.txt`) and hierarchical album structures (`albums.dat`) on startup and commits in-memory mutations back to disk upon exit.
- 📜 **Auditable Command Telemetry**: Logs timestamped user operations to `command_log.txt` via a dedicated logging subsystem (`logger.c`) for session replay and debugging.
- 🛡️ **Defensive Memory & Input Hygiene**: Gracefully handles empty-queue edge cases, dangling pointer prevention during active-track deletion, and safe `fgets`/`sscanf` input parsing.

---

## 🏗️ System Architecture & Data Structures

```text
┌─────────────────────────────────────────────────────────────────────────┐
│                         C-Unplugged CLI Engine                          │
├───────────────────────┬─────────────────────────┬───────────────────────┤
│   Global Song Pool    │   Album Registry        │  Active PlayList      │
│  (Singly Linked List) │  (Nested Singly Lists)  │ (Circular Doubly LL)  │
│                       │                         │                       │
│  [Song 1] -> [Song 2] │  [Album A] -> [Album B] │  ⇄ [Node 1] ⇄ [Node 2]│
│      ↕                │      │                  │    ▲              ▲   │
│   songs.txt           │      └──> [Track List]  │    └──────────────┘   │
│                       │              ↕          │   O(1) Next/Prev/Del  │
│                       │          albums.dat     │                       │
└───────────────────────┴─────────────────────────┴───────────────────────┘
```

### Complexity & Design Breakdown

| Subsystem | Underlying Structure | Time Complexity | Architectural Rationale |
| :--- | :--- | :--- | :--- |
| **Playback Queue (`playlist.c`)** | Circular Doubly Linked List | **$O(1)$** step next/prev, **$O(1)$** active-node removal | Eliminates null-boundary checks during continuous radio looping and allows immediate splicing when removing the currently playing track. |
| **Global Song Library (`song.c`)** | Singly Linked List | **$O(1)$** tail append, **$O(N)$** indexed lookup | Dynamic heap allocation scales to arbitrary catalog sizes without fixed-size array reallocations. |
| **Album Catalog (`album.c`)** | 2-Level Hierarchical SLL | **$O(A \cdot S)$** serialization | Each `Album` node maintains its own internal `Song` linked list, decoupling album curation from the active playback queue. |
| **Audit Logger (`logger.c`)** | Append-Only File Stream | **$O(1)$** amortized write | Flushes timestamped menu commands (`ctime`) to `command_log.txt` for persistent session history. |

### Repository Layout

```text
C-unplugged/
├── common_structures.h   # Core structs (Song, Album, PlayList) & ANSI color macros
├── main.c                # Interactive CLI state loop & menu dispatcher
├── song.h / song.c       # Master song library CRUD & songs.txt serialization
├── album.h / album.c     # Album catalog management & albums.dat persistence
├── playlist.h / .c       # Circular Doubly Linked List queue & cursor mechanics
├── song_controls.h / .c  # Playback state transitions & track navigation
├── logger.h / logger.c   # Timestamped command history logger (command_log.txt)
├── songs.txt             # Persistent master track database
├── albums.dat            # Persistent album-to-track mapping store
└── Makefile              # GNU Make build automation
```

---

## 🚀 Getting Started

### Prerequisites

- **C Compiler**: `gcc` or `clang` (supporting C99/C11)
- **Build System**: GNU `make`

### Compilation & Execution

```bash
# 1. Clone the repository
git clone https://github.com/voidp1xel/C-unplugged.git
cd C-unplugged

# 2. Clean any previous build artifacts and compile
make clean && make

# 3. Launch the interactive CLI
make run
# or directly:
./c_unplugged
```

---

## 🎮 Interactive CLI Operations

| Option | Command | Description |
| :---: | :--- | :--- |
| `1` | **List All Songs** | Displays all tracks registered in the global song pool with durations. |
| `2` | **List All Albums** | Prints all curated albums and their release years. |
| `3` | **Create Album** | Allocates a new album entry and persists it to the catalog. |
| `4` | **View Album Songs** | Inspects the tracklist of a specific album. |
| `5` | **Add Song to Album** | Links tracks from the global pool into a target album. |
| `6` | **Delete Song from Album** | Unlinks and frees a specific track node from an album. |
| `7` | **Add Album to Playlist** | Batch-enqueues an entire album into the Circular Doubly Linked List queue. |
| `8` | **Add Song to Playlist** | Enqueues an existing library song by index or registers a new track on the fly. |
| `9` | **Play Next** | Advances the playback pointer (`current->next`) with circular wrap-around. |
| `10` | **Play Previous** | Reverses the playback pointer (`current->prev`) in $O(1)$ time. |
| `11` | **Remove Current Song** | Unlinks and `free()`s the active node from the circular queue safely. |
| `12` | **View Command History** | Streams the session audit log from `command_log.txt`. |
| `0` | **Save & Exit** | Serializes all in-memory mutations to `songs.txt` and `albums.dat` and exits cleanly. |

---

## 📜 License & Copyright

**© 2026 Aniket Goel. All Rights Reserved.**

This software and its underlying source code are proprietary and confidential. It may not be reproduced, distributed, or modified in any form or by any means without explicit prior written permission from the author.
