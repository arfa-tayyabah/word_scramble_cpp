# Word Scramble 🔤

A graphical word-unscrambling game for Windows, built in C++ with the WinBGIm
(Borland Graphics Interface) library. Sign up, race the clock across three
difficulty tiers, unscramble 60 hand-picked words, and climb a persistent
leaderboard.

![platform](https://img.shields.io/badge/platform-Windows-blue)
![language](https://img.shields.io/badge/language-C%2B%2B-00599C)
![graphics](https://img.shields.io/badge/graphics-WinBGIm-orange)
![status](https://img.shields.io/badge/status-active-brightgreen)

## Overview

Word Scramble is a single-player desktop game where players unscramble
randomly shuffled words across three progressively harder levels. Each level
presents 20 words drawn from its own category set, with hints, a shuffle
button, a live timer, and a limited number of attempts. Scores are calculated
from difficulty, speed, and hint usage, then saved to a shared leaderboard
alongside per-user progress that persists between sessions.

## Features

- 🎮 **Three difficulty levels** — Easy, Medium, and Hard, each with 20 unique
  words, its own attempt/hint/shuffle budget, and its own background theme
- 🔐 **Account system** — sign up with a username, Gmail-validated email,
  password, and recovery PIN; log back in later to resume progress
- 🔑 **Password recovery** — forgotten passwords can be recovered via PIN
  verification
- 💡 **Smart hints** — request a random category, first-letter, or last-letter
  hint (limited per level, and each use costs points)
- 🔀 **Word shuffling** — re-scramble the current word if you're stuck
  (limited uses per level)
- ⏱️ **Live timer** — tracks time spent on each word; slower solves cost points
- 🏆 **Persistent leaderboard** — top scores across all players are saved to
  disk and re-sorted every game, with the current player highlighted
- 💾 **Per-user save files** — score, level, and progress are saved after
  every word so a session can be resumed later
- 🔊 **Sound & visuals** — background music, button/win/loss sound effects,
  and a unique background image per level
- 🖱️ **Mouse + keyboard driven UI** — click-through menus and buttons, typed
  word entry

## How to play

1. Launch the game and click **Play** on the title screen.
2. **Sign up** (username, Gmail-format email, password, recovery PIN) or
   **sign in** with an existing account.
3. Solve the scrambled word shown on screen:
   - Type your answer and press **Enter** to submit.
   - Click the **Hint** button for a category / first-letter / last-letter
     clue (limited uses, costs points).
   - Click the **Shuffle** button to re-scramble the letters (limited uses).
4. Each incorrect guess costs one attempt; run out of attempts and it's
   **Game Over**.
5. Clear all 20 words in a level to advance: **Easy → Medium → Hard**.
6. Solve all three levels to see **YOU WON!** — either way, your final score
   is added to the leaderboard.

### Scoring

Each level has a base score, a time penalty for slow answers, and a penalty
per hint used:

| Level  | Base score | Time penalty kicks in after | Hint penalty |
|--------|-----------:|------------------------------|-------------:|
| Easy   | 4 pts      | 30 sec                       | −1.5 / hint  |
| Medium | 7 pts      | 60 sec                       | −1.5 / hint  |
| Hard   | 10 pts     | 90 sec                       | −1.5 / hint  |

Scores never drop below 0 for a single word. The leaderboard keeps only each
player's best score, so repeat attempts only help.

## Controls

| Input | Action |
|---|---|
| Keyboard (typing) | Enter your unscrambled word / form inputs |
| `Enter` | Submit current input |
| `Backspace` | Delete last character |
| Mouse click | Navigate menus, click **Shuffle** / **Hint** buttons |

## Requirements

This project uses **WinBGIm**, a Windows-only wrapper around Borland's
classic graphics library (`graphics.h`), plus the Windows Multimedia API for
sound. It will **not** build or run on macOS or Linux without significant
porting.

- Windows OS
- A MinGW-based C++ compiler (e.g. **Dev-C++** or **Code::Blocks with MinGW**)
- [WinBGIm](https://www.cs.colorado.edu/~main/bgi/cs1300/) graphics library
  installed and linked
- Windows Multimedia library (`winmm`) for `PlaySound`

## Building

The easiest path is **Dev-C++** with WinBGIm pre-installed:

1. Install [Dev-C++](https://www.bloodshed.net/) (or Orwell Dev-C++) and the
   [WinBGIm](https://www.cs.colorado.edu/~main/bgi/cs1300/) add-on, following
   the installer's instructions to copy `graphics.h` / `winbgim.h` and the
   BGI libraries into your compiler's `include` and `lib` folders.
2. Open `main.cpp` in Dev-C++ (or add it to a new project).
3. In **Project Options → Parameters → Linker**, add:
   ```
   -lbgi -lgdi32 -lcomdlg32 -luuid -loleaut32 -lole32 -lwinmm
   ```
4. Build and run (F9 / F11).

### Building from the command line (g++ / MinGW)

```bash
g++ main.cpp -o WordScramble.exe -lbgi -lgdi32 -lcomdlg32 -luuid -loleaut32 -lole32 -lwinmm
```

Adjust library names/paths to match your WinBGIm installation.

## Required assets

The game loads the following files from its working directory at runtime.
They are **not included in this repository** — supply your own (or the
originals used during development) before running:

**Images** (`.bmp`, sized to the window / regions used in-code):
- `login.bmp` — login screen banner
- `valley.bmp` — main menu / transition background
- `beach.bmp` — Easy level background
- `rainbow.bmp` — Medium level background
- `waterfall.bmp` — Hard level background

**Sounds** (`.wav`):
- `start1.wav` — title screen sting
- `button.wav` — button click
- `gameloop.wav` — looping background music during gameplay
- `game-over.wav` — played on loss

Place them all alongside the compiled executable.

## Data files

The game creates and reads several plain-text files in its working directory
— back these up if you want to preserve accounts, progress, or rankings:

| File | Purpose |
|---|---|
| `users.txt` | Registered accounts: username, email, password, and recovery PIN (plain text — see [Security notes](#security-notes)) |
| `<username>.txt` | That player's saved score, current level index, and stage reached |
| `leaderboard.txt` | Global leaderboard: name, score, and words solved per entry |

## Project structure

This is currently a single-file project:

```
main.cpp    # entire game: UI, accounts, gameplay, scoring, persistence
```

Logical sections within `main.cpp`:
- **Account system** — `Login` class (`signUp`, `login`), email validation,
  password recovery
- **Screens** — `main_screen`, `login_screen`, `screen_flow`, button hit
  testing
- **Word model** — `alphabets`, `word` classes; per-level word/category/hint
  arrays
- **Gameplay** — `Gamelevel` base class and `Easylevel` / `Mediumlevel` /
  `Hardlevel` subclasses (scrambling, input handling, hints, shuffling,
  timing)
- **Scoring & persistence** — `calculateScore`, `saveUserProgress` /
  `loadUserProgress`, `saveAndLoadLeaderboard`, `showGameEndMenu`

## Security notes

This is a learning/portfolio project, and its account system reflects that:
passwords and recovery PINs are stored in **plain text** in `users.txt`. Do
not reuse real passwords when creating an account, and don't treat this as a
model for production authentication.

## Known limitations

- Windows-only (WinBGIm has no macOS/Linux equivalent without porting to
  SFML, SDL2, or similar)
- Credentials stored unencrypted in plain text
- Fixed word lists (20 per level) rather than a randomized/expandable bank
- No window resizing — fixed `1000×650` canvas
- Background music loops via `PlaySound`, which can only play one waveform
  audio stream at a time on some systems, occasionally interrupting sound
  effects

## Roadmap ideas

- [ ] Port rendering to SDL2 for cross-platform support
- [ ] Hash stored passwords instead of storing them in plain text
- [ ] Externalize word lists into a data file so new categories/levels don't
      require recompiling
- [ ] Add a difficulty-select / practice mode that doesn't affect the
      leaderboard
- [ ] Add unit tests for scoring and word-scrambling logic
