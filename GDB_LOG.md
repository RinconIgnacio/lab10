# GDB log

One dated section per week. Paste the commands you used and the output you saw, then explain what it means in your own words. Pasted output with no explanation earns partial credit at most.

## Week 10 (Lab 5)

### Part A: basics with `buggy_rooms.cpp`

Build and run:

```
cmake -S . -B build
cmake --build build --target buggy_rooms
./build/buggy_rooms
```

Record:

- The backtrace (`bt`) after the crash:
- The function and line number where your own code crashed (use `frame N` to move to it):
- The value of the pointer that caused the crash (`print`):
- How you stepped through the program (`break main`, `run`, `next`, `step`): list the commands and what each showed:
- The fix, and how you checked it:

### Part B: memory with `deck_bugs.cpp`

Build with AddressSanitizer and run each bug on its own (`./deck_bugs 1`, `2`, `3`).

For each bug, write one or two sentences: what kind of error AddressSanitizer reported, where the memory was allocated, and where it was freed.

| Bug | Report kind | Allocated at | Freed at | Cause in one sentence |
| --- | --- | --- | --- | --- |
| 1 | | | | |
| 2 | | | | |
| 3 | | | | |

Confirm one bug in GDB. Use at least two of `watch`, `finish`, and `x`. Paste the session and explain what each command showed:

Fixes for all three bugs (describe each change):

### Debugging session — October 23, 2026

#### Part A: buggy_rooms.cpp

Commands: break main, run, next, step, continue, bt, frame 1, print room.

The breakpoint stopped at main(), line 39. After continuing,
the program printed the hall, kitchen, and pantry descriptions
before crashing with SIGSEGV.

The backtrace showed describe() at line 36, called from main()
at line 53. The command print room showed (const Room *) 0x0.

The problem was a null pointer. I fixed the movement loop by
checking whether the next room exists before calling describe().

#### Part B: deck_bugs.cpp

| Bug | Report kind | Allocated at | Freed at | Cause |
| --- | --- | --- | --- | --- |
| 1 | Memory leak (416 bytes) | deal_a_hand(), line 40 | Never freed | The hand array was not deleted. |
| 2 | Double-free from shallow copy | RawDeck constructor, called from line 47 | RawDeck destructor, called from line 50 | Two objects shared the same array. |
| 3 | Double-free | clear_a_table(), line 54 | Lines 57 and 59 | The same pointer was deleted twice. |

GDB commands used: break copy_a_deck, run 2, next,
x/2gx &original, x/2gx &backup, finish.

The x command examined the memory occupied by each RawDeck
object. The finish command continued execution until
copy_a_deck() returned to main(). The program printed
"Backup top card: AC" and returned without crashing.

Fixes:
1. Added delete[] hand to release the allocated array.
2. Added a deep-copy constructor to RawDeck.
3. Removed the second delete[] table statement.

GDB observations:
The x command examined the memory of the RawDeck objects.
The finish command completed copy_a_deck() and returned to main().
The program printed "Backup top card: AC" without crashing.

Fixes:
1. Added delete[] hand to fix the memory leak.
2. Added a deep-copy constructor to prevent shared ownership.
3. Removed the second delete[] table to prevent double-free.

Verification:
All seven regular tests and all seven AddressSanitizer tests passed.
All three deck_bugs cases ran without sanitizer errors.
