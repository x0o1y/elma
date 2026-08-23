# Game release
This is NOT a playable release of Elasto Mania. If you're looking to play any of the Elasto Mania games, visit https://elastomania.com.

# Elasto Mania (2000) Source Code
This repository was uploaded in good faith for the purpose of exploring the original source code of this classic game. Non-source-code game assets (sounds, graphics, tools, etc.) are not part of this open source release.

See LICENSE.md for license information.

If you'd like to use this source code in ways other than permitted by the license and this document, contact us at info@elastomania.com.

If you'd like to support continued development of the Elasto Mania franchise, you can do so by buying our games on any store front linked on our website.

*The Elasto Mania Team*

https://elastomania.com

## Elma Amplified

Elma Amplified is a native Apple Silicon port of the original Elasto Mania
engine with the Keyamp keyboard model built into the game. It is maintained by
`em` / `x0o1y`: Elasto Mania, Emacs and Egor Maltsev meet in one executable.

The original game and physics code compile into a Mach-O arm64 binary. Wine,
DOSBox and keyboard-remapping utilities are not involved. SDL2 supplies the
macOS window, display, input and timing backend.

Current status:

- native video, keyboard, mouse and timing;
- Keyamp gameplay and menu navigation;
- canonical single-key alovolt and common Elma aliases;
- original arrow controls remain available;
- audio is temporarily disabled.

### Build on Apple Silicon macOS

Install the build tools and SDL2 compatibility library:

```sh
brew install cmake pkg-config sdl2-compat
```

Clone and compile:

```sh
git clone https://github.com/x0o1y/elma.git
cd elma
cmake -S . -B build -DCMAKE_CXX_COMPILER=/usr/bin/clang++
cmake --build build -j8
file build/elma
```

The last command must report a Mach-O 64-bit arm64 executable. The legacy
source produces many compiler warnings; a successful build currently ends with
`Built target elma`.

The default build selects the original shareware profile. This matches the
freely distributable shareware data and keeps the source release usable without
pretending that it contains the registered game assets.

### Add game data

The upstream source release deliberately excludes copyrighted game data. Copy
the following files from a legally obtained Elasto Mania installation into the
checkout:

```text
elma.res
lgr/default.lgr
```

Prepare the writable runtime directories:

```sh
mkdir -p lev rec snaps
```

The resulting layout begins like this:

```text
elma/
├── build/elma
├── elma.res
├── lgr/default.lgr
├── lev/
├── rec/
└── snaps/
```

Game data, generated state, levels, replays, screenshots and build output are
ignored by Git and must not be committed.

### Run

Run the binary from the repository root because the original engine resolves
its data paths relative to the working directory:

```sh
./build/elma
```

`state.dat` is generated automatically on the first run.

### Keyamp gameplay

The complete bike is controlled under the right hand:

```text
I        throttle
K        brake
J        rotate left
L        rotate right
;        alovolt
Space    turn
```

Additional amplifier bindings:

```text
E        brake alias
P        screenshot
Q        escape alias
F        Enter alias
```

Alovolt is implemented as an actual simultaneous hold of both rotate states.
It is not a prerecorded or timed macro: the move is still produced by the
original Elma physics bug. Independent reference counting keeps overlapping
aliases correct when one physical key is released before another.

### Keyamp interface

Menus use the same movement model:

```text
I / K    previous / next
J / L    left / right
F        confirm
Q        escape
D        delete
```

The commands use physical SDL scancodes and therefore stay in place when the
input language changes. Shift plus a command letter inserts the literal
uppercase letter during text entry, following Keyamp command-mode behavior.

The original arrows, Enter, Escape, Backspace and Space continue to work.
