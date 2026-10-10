# Commando — kit feedback

## Skill text that changed what I did

- `60-verify`: "audit the whole listing before the page is published": the first sample found 12 of 70 wrong, so four fresh agents audited all 897 comments in disjoint ranges and changed 137.
- `70-minisite`: "Draw from the memory the game draws from": I held each map the page draws against play memory in its own area, which showed the gate and area 1's bunkers that every life stamps into the map, and the page now draws them.
- `60-verify`: "list them all: every caller of the routine, every reader of the variable": listing every write to the area counter during the copy pass showed the name entry, not the score line, sets it to 2.
- `70-minisite`: "Sweep the whole input space, not a few plausible values": the score-line test runs every BCD score, grenade count and lives count, and the box's case in each of the eleven enemy slots, against the game's code.
- `70-minisite`: "Give every paragraph its purpose, in a line written to yourself": the gun and grenade paragraphs left the sprite section for a section of their own.

## What was changed in the kit

`kit/c64/tools.py`, `kit/scripts/launcher.py` and `kit/c64/get_vice.py`: the launcher adds `tools/vice-libs/usr/lib/x86_64-linux-gnu` to the emulator's library path, and to the check that names missing libraries, whenever that folder exists, so the release zip can run on Ubuntu 26.04 with 24.04's `libFLAC.so.12` unpacked there and nothing installed on the system.

`kit/c64/test_vice_environment.py`: tests that the library path stays the child process's and that the missing-library check uses it.

`kit/lessons/2026-10-10-commando.md`: the index register a handler's callees leave behind, a loop's count against what it shows, and a drawn map held against play in its own area.

`kit/c64/INSTALL.md` and `site/status.json`: the Ubuntu 26.04.1 route through `tools/vice-libs`, which passed 57 of 57 on 6 October 2026.

## Candidates

- The third lesson in `kit/lessons/2026-10-10-commando.md`, a drawn map held against play in its own area, would sharpen `70-minisite`'s "Draw from the memory the game draws from", which names only the character set and the colour table. Only this game shows it so far.
- A page sentence about how the music sounds (which voice carries the bass and the drums) went into the draft from a reading of the driver and was wrong. Running the tune through the port and counting each voice's instruments and notes settled it. Only this game shows it so far.

## Maintainer asks

- #318: this run is another data point: a first sample of 17 % led to a full audit, which changed 137 of 897 comments (15 %), and a second sample then found 1 of 70 wrong (1.4 %).
- **coverage.py: say why each run in the work queue is still bare.** Most of the time this run spent getting from 52.8 % to 100 % went on working out why a stretch next to a commented symbol did not count: an auto symbol inside a table that needed its own comment, a data symbol's 64-byte reach, a span cut at a fixed memory-map edge. The work queue prints the address, the size, the kind and the nearest symbol. A column naming the reason (no comment on the symbol that owns it, beyond its reach, cut at an edge) would point at the fix. It goes in `kit/scripts/coverage.py`, beside the queue; from Commando (C64), 10 October 2026, branch `game/c64/commando`.

## What cost the most time

Getting coverage from 52.8 % to 100 %: a work queue that said why each stretch was still bare would have saved the most.
