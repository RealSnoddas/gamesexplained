# Commando — features

Read this before annotating code. What the game is documented to do, with
verification status against the binary. Statuses: **open** (documented,
not found yet), **traced** (in the code, could not be exercised; say what
was tried), **confirmed** (in the code, consistent with the emulator),
**live** (observed directly), **differs** (the code does something else).
"Absent" is not a status.

Sources:

- [Commando – C64-Wiki](https://www.c64-wiki.com/wiki/Commando), read 2026-10-06 and again 2026-10-10: credits, controls, enemies, bugs, the extra-life rule, the loop after the third area, and the versions list, which describes this image's crack.
- [Commando / Space Invasion](https://www.worldofspectrum.org/pub/sinclair/games-info/c/Commando_English.pdf), Elite Systems' multi-format instructions (1985), read 2026-10-10: the story, the starting kit, the C64 controls and the object.
- [Commando +5HD by Remember](https://csdb.dk/release/?id=40672), CSDb, read 2026-10-10: the crack's date (June 1997) and its credits, including a bug fix.
- [Commando](https://www.gamebase64.com/game.php?id=1602), GameBase64, read 2026-10-10: programmer, graphics and music credits.
- Lemon64's instructions and review (`lemon64.com/doc/commando/148`, `lemon64.com/review/commando/30`) were read on 2026-10-06; on 2026-10-10 the site answered every fetch with a browser check (HTTP 403).

## Features

| Feature | Status | Where |
|---|---|---|
| Move Super Joe through a vertically scrolling battlefield | live | Port-2 up scrolls terrain while the player stays near the same screen coordinates; port-2 left moved the player left in a short run. The full eight-direction input map is not verified. |
| Start the game with joystick fire | live | Port-2 fire starts play from the title screen. |
| Fire the M60 machine gun with the fire button | open | Elite's instructions and C64-Wiki. |
| Throw grenades with Space | live | Space reduced the HUD count from 5 to 4 and a grenade projectile appeared; code still to trace. |
| Start with six hand grenades | open | Elite's instructions say six; the count shown at the start of play is still to read. |
| Collect flashing boxes to replenish the grenade count | open | C64-Wiki; Elite's instructions call them supplies of hand grenades from defeated outposts. |
| Fight through three areas, each ending at a fortress where enemies storm out in waves | open | C64-Wiki and Lemon64. |
| Enemies include soldiers, bazooka users, vehicles, and soldiers behind walls who leave cover as the player passes | open | C64-Wiki and Lemon64. |
| A lost life restarts the player shortly before the place he was hit | open | C64-Wiki. |
| An extra life every 10,000 points | open | C64-Wiki, "Solution". |
| After the third area the game starts again, alternating between normal enemy fire and slightly faster fire | open | C64-Wiki, "Solution". |
| The score wraps from 999,999 to 000,000, in the high-score list too | open | C64-Wiki, "Bugs". |
| The grenade count wraps after 99 | open | C64-Wiki, "Bugs". |
| Lives count to 256 in binary but show as decimal digits, so a large count shows graphics characters | open | C64-Wiki, "Solution". |
| Sprites flicker and the game slows down when many enemies are on screen | open | C64-Wiki, "Bugs". |
| Play the title score during gameplay | confirmed | SID state showed changing frequencies on all three active voices during play; `$5012` and `$50AA` are the sequence tick and per-channel updater. Listening and mapping the score sequence remain open. |
| Save the high-score list to disk, with a reset option | confirmed | REMEMBER's addition, not Elite's: C64-Wiki lists "resettable highscore list that is saved on disk" among the features of this crack, and the file is `COMMANDO HI /REM`. `$44E0` calls KERNAL SAVE for `@S:COMMANDO HI /REM`, saving `$0EED-$0F47` (91 data bytes). The payload matches the supplied 93-byte PRG including its two-byte load address; the save path has not been exercised live. |
| Up to five trainer options, chosen one by one | live | REMEMBER's addition. The crack's menu identifies `Commando 100% +5`; no trainer option selected. |

## Beyond the documentation

Nothing established yet.

## Open questions

- Which routines unpack the REMEMBER release, initialize the engine, and install the IRQ handler?
- Does this disk image implement all three areas in resident memory, or reload any game data?
- CSDb credits the crack with a bug fix. Which bytes did REMEMBER change, and what did the fix change for the player?
