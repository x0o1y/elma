# Elma fork instructions

## Project

This is the original Elasto Mania source with a native macOS Apple Silicon
port. Preserve the original game and physics behavior. Platform integration
belongs in `F_MAC/`; portable build configuration belongs in
`CMakeLists.txt`.

The repository contains source code only. Never add `elma.res`, LGR data,
levels, replays, screenshots, generated state, build directories or other
game assets. Do not weaken or bypass the upstream licensing boundary.

## macOS build

Build with Apple Clang and SDL2:

```sh
cmake -S . -B build -DCMAKE_CXX_COMPILER=/usr/bin/clang++
cmake --build build -j8
file build/elma
```

The result must be a Mach-O arm64 executable. The default CMake profile defines
`SARVARI` because the documented freely distributable data is the shareware
data. Run the executable from the repository root so relative asset paths keep
working.

The legacy source currently emits many warnings about writable string literals,
deprecated C functions and filename case. A successful build may contain those
warnings; do not mix broad warning cleanup into functional changes.

## Keyamp input

`F_MAC/MAC_PLATFORM.CPP` is the single macOS keyboard translation layer.
Keyamp uses physical SDL scancodes, so commands stay on the same physical keys
when the input language changes.

The gameplay bindings are:

```text
I        throttle
K        brake
E        brake alias
J        rotate left
L        rotate right
H        supervolt (right, then both rotations)
;        alovolt
Space    turn
P        screenshot
```

The interface bindings are:

```text
I/K      previous/next
J/L      left/right
F        confirm
Q        escape
D        delete
```

Shift plus a command letter inserts the literal uppercase letter. Preserve this
when changing text input.

Alovolt is not a timed macro. While `;` is held, both rotate states must be held
simultaneously so the original physics code produces the move. Key aliases use
reference counts: releasing one physical alias must not clear a logical action
that another held key still supplies. Preserve arrow-key compatibility.

Supervolt on `H` starts with right rotation and adds left after one completed
physics step. Keep this sequence tied to physics steps, not wall-clock delays
or the number of keyboard polls. Releasing `H` removes only its own aliases.

## Verification

For every source change:

1. Build from a fresh or correctly configured build directory.
2. Run `git diff --check`.
3. For input changes, launch the game and manually test menus, text entry,
   ordinary rotation, alovolt and overlapping aliases.
4. Do not commit or push until the requested behavior has been observed in the
   running game.

