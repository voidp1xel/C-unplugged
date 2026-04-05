# 🎵 C-Unplugged Music Application 🎵

**C-Unplugged** is a vibrant, fully-featured, CLI-based music library and playlist manager written entirely in C. Step away from the heavy GUI apps and return to the golden age of terminal utilities, with modern features, colorful outputs, and highly interactive menus.

---

## 🌟 Key Features

- 🎧 **Spotify-like "Now Playing" UI**: Any active song running in your playlist visually displays in a dedicated, colorful `▶ NOW PLAYING:` header right on the main menu.
- 🔁 **Looped Playback**: Enjoy infinite radio! Backed by a structural **Circular Doubly Linked List**, the playlist organically loops back to the start when you hit 'next' on your final track. 
- ⚡ **Interactive Library Expansion**: Effortlessly add to your playlist by typing a song's automatically-generated serial number—no more spelling out full case-sensitive titles!
- 💾 **Persistent Storage**: Whether you are modifying `.txt` lists directly or manually adding songs through the CLI app, your entire custom library (songs and albums) is reliably serialized back to `songs.txt` and `albums.dat` on exit.
- 📜 **Session Logging**: All command choices are safely logged on disk so you can review interaction history any time.
- 🛡️ **Robust Edge Cases**: Fully polished safety checks. Deleting from empty playlists or viewing missing libraries handles gracefully via custom colored alerts rather than crashing.

---

## 🏗️ Architecture & Code Details

Under the hood, C-Unplugged is built using clean and efficient data structures to simulate a true desktop music player:
- **Circular Doubly Linked List:** Powers the customizable `PlayList` engine, ensuring fast inserts, O(1) removals for the current node, and seamless looping between the head and tail of the queue.
- **Singly Linked Lists:** Act as scalable dynamic memory pools for tracking the global `Album` and `Song` libraries.
- **File System Serialization:** Complex data structures are intelligently traversed and baked down into raw persistent disk files `.txt` and `.dat` formats via `save_album_to_file()` and `save_songs_to_file()`.
- **Modular Design:** Subdivided comprehensively into discrete C headers (`logger.h`, `song.h`, `album.h`, `playlist.h`, `song_controls.h`) optimizing code maintainability and testability.

---

## 🚀 Getting Started

### Prerequisites

- A C Compiler (e.g., `gcc` or `clang`).
- GNU `Make`.

### Building the Project

Navigate to the project root directory and build the application cleanly using Make:

```bash
# Clean previously built objects and compile the project
make clean
make
```

### Running the Program

To run the compiled program, simply execute the generated binary directly, or use the `Make` shorthand:

```bash
make run
# OR
./c_unplugged
```

---

## 🎮 How to use

Upon starting the program, you will be presented with the main menu. 
Enter the number corresponding to your desired action and press `Enter`. 

**Options:**
1.  **List all songs:** Displays every song loaded into your library.
2.  **List all albums:** Displays the names and release years of your created albums.
3.  **Create album:** Prompts you for an album title and release year. Adds an empty album entry to your library.
4.  **View album songs:** Prompts for the name of an existing album and lists the specific songs contained within.
5.  **Add song to album:** Bulk adds tracks from your global library directly into an album's catalog.
6.  **Delete song from album:** Search and remove a singular track from a given album without deleting the album itself.
7.  **Add album to playlist:** Bulk inserts all tracks belonging to a given album to your 'Now Playing' queue.
8.  **Add song to playlist:** Generates a sub-menu! You can instantly enqueue a song by its serial number or generate a brand-new track on the fly.
9.  **Play next:** Moves the playlist cursor forward and loops if at the end of the queue.
10. **Play previous:** Reverses the playback cursor natively.
11. **Remove current song from playlist:** Deletes your currently highlighted track from the playlist natively.
12. **View command history:** Displays prior session menu interactions from a local logger.
0.  **Exit:** Safely dumps your modified library state back to disk memory and exits the CLI.

> **Note:** For exact title/text inputs, be sure to spell the title accurately as trailing whitespaces and casing matters!

---

## 📜 License & Copyright

**© 2026. All rights reserved.**

This software and its underlying source code is my proprietary, confidential work.
**It may not be reproduced, distributed, or modified in any form or by any means without my explicit prior written permission.** 
All rights not expressly granted are reserved.
