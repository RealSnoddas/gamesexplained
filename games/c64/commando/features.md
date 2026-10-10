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
| Move Super Joe through a vertically scrolling battlefield | live | `$3A8C`: eight directions; up scrolls the map once he is near the top of his zone. In 500 frames walking up, the screen moved 60 map rows. |
| Start the game with joystick fire | live | `$0F48` waits for fire alone on port 2. |
| Fire the M60 machine gun with the fire button | live | `$39C3`: two bullets for each press, the second two or three frames after the first. Four presses made two bullets each. |
| Throw grenades with Space | live | `$38A5`, reading the keyboard row at `$DC01` through REMEMBER's hook `$5F6A`. The count fell by one and the grenade appeared. |
| Start with six hand grenades | differs | Five: `$0883` sets `$04FF` to 5, read live at the start of play, and every life tops it up to five (`$3DE0`). |
| Collect flashing boxes to replenish the grenade count | live | `$2A5A`: three grenades and 1,000 points; the box flashes between two colours every 8 frames. A box put under the player in slot 5 added them. |
| Fight through three areas, each ending at a fortress where enemies storm out in waves | confirmed | Areas 0, 1 and 3 are played (`$0939`); the first two end at a gate in a wall, the third at the fortress. Twenty soldiers come out at the top, one chance in 128 a frame (`$2CBB`), and the area is won when all have come and none is left (`$3A8C`). The area order and the messages between them were seen live. |
| Enemies include soldiers, bazooka users, vehicles, and soldiers behind walls who leave cover as the player passes | traced | 41 behaviours (`$24D4`): rifle soldiers, jumpers, grenade throwers, bomb throwers, mortar men in trenches, snipers, foxholes, doors that let soldiers out, bunker guns, a motorcycle and sidecar, a lorry that drops soldiers, a jeep, guards with a prisoner. Soldiers lying in wait get up when the map has carried them down to y `$82` (`$2FA4`). Doors, the mortar man and one grenade thrower are only in the unused fourth spawn list (`$1D42`) and never appear. |
| A lost life restarts the player shortly before the place he was hit | confirmed | `$3DE0` restarts at the first checkpoint at or below the row reached: rows 19, 61, 97, 131 and the area's start (`$3EBC`). Every area began at its last checkpoint, live. |
| An extra life every 10,000 points | live | `$134F`: each carry out of the score's low byte. 9,900 plus 1,400 gave a life. |
| After the third area the game starts again, alternating between normal and slightly faster enemy fire | live | The area counter goes on to 4 and area 0 again; the fire mask (`$3EB4`) lets a soldier fire every 64, 32 and 16 frames in the first loop and every 16 in the second, then repeats. The masks `$3F`, `$1F`, `$0F`, `$0F` were read live. |
| The score wraps from 999,999 to 000,000, in the high-score list too | live | The score counts hundreds in four BCD digits (`$134F`), so it wraps after 999,900: 999,900 plus 1,400 showed 001,300, and gave a life. The high-score table keeps the same two bytes (`$0F36`). |
| The grenade count wraps after 99 | live | Two BCD digits, and the box's add drops the carry (`$2A90`): 98 plus a box made 01. |
| Lives count to 256 in binary but show as decimal digits, so a large count shows graphics characters | live | `$13F1` prints the binary count's two hex digits as glyphs: ten lives showed 0 and glyph `$2B`. |
| Sprites flicker and the game slows down when many enemies are on screen | traced | The main loop waits for the next frame tick (`$400C`), so a pass longer than a frame waits for the one after. The multiplexer reuses hardware sprites at lines worked out from the sorted slots (`$41A7`, `$4266`, `$436B`), whatever the slots' positions. A quiet stretch ran 497 passes in 500 frames; a crowded one was not measured. |
| Play the title score during gameplay | confirmed | Tune 0, started at the title and again at each area (`$0883`, `$08B8`). All three voices changed during play. |
| Save the high-score list to disk, with a reset option | confirmed | REMEMBER's addition, not Elite's (C64-Wiki lists it among the crack's features). `$44E0` saves `$0EED-$0F47` to `@S:COMMANDO HI /REM` after a name is entered; the menu's L loads the file, and R saves the table in memory over it (`$4621`, `$44FF`). The saver ran in a recorded session; the drive's write was not checked. |
| Up to five trainer options, chosen one by one | live | REMEMBER's addition (`$4531`): unlimited lives, unlimited grenades, no falling, no collisions, and a C= key that ends the area. The lives and collision patches kept the player alive through the area tests. |

## Beyond the documentation

- An enemy fires only when he already faces the player's direction, classed into eight by a box 30 pixels across and 20 up and down (`$33B2`, `$310A`). Inside the box he neither turns nor fires.
- The x test of that box adds 30 to the low byte of the soldier's x and drops the carry, so a soldier at x 226-255 takes a player on his left for one on his right, faces right and fires right. *Live:* a soldier held at x `$E8` with the player level at x `$64` fired every bullet to the right; at x `$C8` he fired at the player (`work/scripts/aimbug.py`).
- A box of grenades whose 1,000 points bring an extra life pays out twice, six grenades and 2,000 points, and whatever is in enemy slot 0 vanishes: the extra life's sound request zeroes X before the box frees its slot (`$2A5A`, `$2495`). *Live:* 9,500 points and five grenades became 11,500 and eleven (`work/scripts/boxbug.py`).
- The green soldier leaves a sprite reading 2000, and the score goes up by 1,400 (`$23E7`, `$254F`).
- Shooting both guards frees the prisoner, who waves for 100 frames and earns nothing (`$23E7`, `$2ABC`).
- A grenade's blast lasts 20 frames and kills whatever walks into it (`$371E`).
- Area 1's bunkers come back whole every life, after a grenade has ruined them (`$3DE0`).
- A new area parks the map at row 165, and the life's set-up moves it straight down to a checkpoint: play starts at row 175 in area 0 and 166 in the others (`$3EBC`).
- Areas are numbered 0, 1 and 3. Area 2 has an entry in every per-area table, but the counter skips it (`$0939`): its map and restart rows are area 1's, its spawn list and terrain area 3's, and its character set is the font (`$3EB0`, `$3ED0`, `$1485`, `$14A5`, `$1427`). An unused fourth spawn list (`$1D42`) and terrain table (`$198B`) are in memory.
- The attract mode shows seven views of the battlefield, 255 frames each, between showings of the credits (`$0FAD`). Its loop sets up and draws an eighth view from one byte past each of its tables (area counter `$53`, row 160, `$1001`, `$1008`), then jumps straight back to the title, which draws the credits over it. *Live:* stopped as each view was set up, the eighth read row `$A0` and counter `$53`, and 60 frames later the screen showed the credits.
- Doors that let soldiers out (spawn handlers 23 and 24), the mortar man (26) and one of the grenade throwers (10) are in the code but in none of the three spawn lists in use, only in the unused fourth (`$1D42`), so they never appear in play. The mortar man's recoil flag is shared with the trench soldiers (`$2579`), and with no mortar man and one trench soldier in the lists in use, the sharing never shows.
- The vehicle that crosses from the side drifts down the screen: its bob back up, copied from the motorcycle's, compares a timer masked to four bits with `$10`, which it can never equal (`$26BF`).
- The name entry is a shooting gallery: the player shoots the letters with a gunsight he steers (`$0C88`).
- REMEMBER's C= skip works only where a grenade could be thrown, because the key is read in the grenade's routine (`$5F6A`).

## Open questions

- CSDb credits REMEMBER with a bug fix. Which bytes changed, and what the fix changed for the player, needs a copy of the 1985 release to compare.
- The eight NOPs at `$0861-$0868`, and the target of the jump at `$0A4A` before REMEMBER pointed it at the saver, need the same comparison.
