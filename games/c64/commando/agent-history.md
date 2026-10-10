# Commando — agent history

Narrative of how the analysis went, including wrong turns, for the next
agent's benefit. This is the only file that narrates; `facts.md` and
`features.md` state current truth only.

## The first run, 6 October 2026

RealSnoddas ran the kit at 0.0.93 on an Ubuntu 26.04.1 desktop, on a model
the session did not name. The VICE release zip would not start there
(`libFLAC.so.12` missing); unpacking Ubuntu 24.04's `libflac12t64` into
`tools/vice-libs` and having the launcher add it to the emulator's library
path got the release to pass 57 of 57. That run booted the game, wrote the
orientation and the features, and annotated 52.8 % of the image before
opening the pull request at Bronze.

## The second run, 10 October 2026

Aaron asked for the pull request to be taken to Silver, answering yes to
every question, in a cloud container (Ubuntu 24.04.5, VICE 3.13.2 release).
The image is REMEMBER's "Commando +5HD" (CSDb 40672). The route to the
game's hand-over at `$0850` goes through the crack's menu (H, then L, to
load the saved high scores); a checkpoint armed at `$0850` before L gave
`work/entry.vsf`, the snapshot behind the listing.

Every comment from the first run was rewritten from the bytes rather than
kept, since its model could not be checked, and coverage went to 100 %.
The ledger's rules cost the most time here: a table needs a comment at
every symbol inside it, an auto symbol inside a typed block has to be
renamed to reach further than 64 bytes, and data spans stop at fixed
memory-map edges. The work queue in `coverage.py` and the "data the ledger
does not count" list in `listing.py --entry` together found the last
stretches.

The first sample of the comments (seed 20261010, 70 drawn) found 12 wrong,
17 %. The errors were the usual kind: a caller list missing members, a
condition stated one-sided, a register value given as one nibble, a count
off by one. Four fresh agents then audited every comment in four disjoint
ranges, and changed 137 of 897. Their notes stay in `work/reports/`. A
second sample by another fresh checker (seed 20261011) found 1 of 70
wrong: the enemy loop described as running every frame, the same slip
the first sample caught in the frame counter. A search for the phrase
found fifteen more routines called from the logic pass with the same
claim, and all sixteen were corrected.

Live tests overturned three claims that read well. The attract mode was
written up as eight views; stopping at each view's set-up showed seven,
because the eighth is drawn and wiped by the title in the same pass. Area 2
was said to copy area 3's tables; it takes area 1's map and restart rows
and area 3's spawn list and terrain. And the first area's map drawn from
the hand-over differed from play at the gate and at area 1's bunkers,
which every life's start stamps into the map; drawing the stamps made all
three maps match play memory byte for byte (the comparison for area 1
needed a capture in area 1, since `play.vsf` is in area 0).

Two bugs came out of the reading. The aim test (`$310A`, `$33C7`) adds 30
to an 8-bit x and drops the carry; a soldier held at x 232 faced and fired
away from a player at 100. The box audit noticed that `$5F56` returns with
X = 0, which makes a box that brings an extra life free enemy slot 0 and
pay twice; a box in slot 2 at 9,500 points gave 2,000 points, six grenades
and the life.

The page's ports (the music driver, the aim test, the score line and the
box) were each checked against the game's own code in `kit/c64/cpu6502.js`
over their whole input space before going on the page. The copy pass, read
claim by claim against the code, caught three more: the score line does not
set the area counter to 2 (the name entry does), the bass and drums of the
title tune are on the voice an effect leaves alone, not the ones it takes,
and the points table has 200 for a foxhole that `facts.md` had left out.

A Playwright click on a canvas below the fold reported an empty readout;
the click had landed outside the viewport. Scrolling the element into view
first fixed it.
