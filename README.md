# Pickleball — serve &amp; return depth

A courtside tracker for one game of doubles. It keeps the score and the serve rotation for you,
and records whether **your team's** serves and returns were deep — so you can see, per player,
whether depth is actually winning you rallies.

Single HTML file, no dependencies, no build step. Open `index.html` in a browser, or add it to a
phone home screen.

## What it does

1. **Setup** — enter four names, pick which team to track, who serves first, and the target score.
   Each team's two players are entered by their **starting side** (right/even and left/odd); the
   app needs that to work out the rotation.
2. **Each rally** — log the depth of your serve or your return, then tap which team won. The score,
   server, server number, and court side all update.
3. **Stats** — live, per player and per team, for the tracked team only.

**Nothing is saved.** State lives in memory; refreshing starts a new game. There's no roster, no
match history, no localStorage.

## Scoring

Traditional side-out doubles, game to 11 / 15 / 21, win by 2.

- Only the serving team scores.
- Score is called as three numbers: **serving score – receiving score – server number**.
- The serving team wins a rally → point, partners switch sides, same player serves again from the
  other side.
- The serving team loses → serve passes to the partner (server 2), who serves from where they're
  standing. No point, no switch.
- Server 2 loses → side out. The incoming team's first server is the player on the right when
  their score is even, the left when it's odd.
- The game opens at **0-0-2** — the first serving team gets only one server.

One consequence looks wrong if you half-remember the rule: **the second server does not
necessarily serve from the side matching the score.** Score parity fixes the side only for the
first server of a side-out. Server 2 serves from wherever they happen to be standing, so with an
odd score you'll often see server 2 on the right. That's correct.

## Logging a rally

Each rally is two steps, numbered on screen. The active step is highlighted and the other is
dimmed, so it's always obvious what to tap next.

**Step 1 — depth.** Deep or Short, attributed to whoever is serving or returning for your team.

**Step 2 — who won the rally.** Unlocks once step 1 is logged.

### Buttons that skip step 2

Some outcomes *are* the result, so they record the rally on the spot:

| Button | Shown when | Rally goes to | Effect |
|---|---|---|---|
| **Fault** | you're serving | opposition | Your partner serves (server 1) or side out (server 2). No point. |
| **Return out** | you're receiving | opposition | They're serving, so **they get a point**. |
| **Serve was out** | you're receiving | you | Their partner serves, or side out to you. No point. |

Deep and Short deliberately don't auto-resolve — a deep serve can win or lose the rally, so the
depth says nothing about the outcome.

> **A fault isn't always a side out.** It's a loss of rally. If your *first* server faults, serve
> passes to your partner; only a *second*-server fault is a side out. The app follows the real
> rule, so the score stays in sync with the one being called on court.

### What goes in which bucket

- **Fault** — its own row in the serving table, so a serve into the net doesn't get filed as a
  "short serve that lost the rally" and drag the short-serve rate down for the wrong reason.
- **Return out** — its own row in the returning table. Covers anything that didn't come back:
  hit out, into the net, or aced.
- **Serve was out** — not logged at all. It isn't a return you made, so it only moves the score.

Both third buckets are counted and displayed but excluded from the deep-vs-short comparison.

A running "last rally" line sits above step 1 so you can see what was just recorded. **Undo** steps
back through score, server, side, and stats together, including auto-resolved rallies.

## Reading the stats

**Serving.** You only score while serving, so a won rally here really is a point. Columns are
attempts, points won, points lost, win %.

**Returning.** Winning a rally on your return earns the serve back — a **side out, not a point**.
The column is labelled "Won" rather than "Pts won" for that reason. If you want to know whether
deep returns produce points, this is the closest honest proxy: it measures whether the return won
the rally, not what happened during the service turn that followed. Rallies where their serve went
out don't appear here at all — you didn't make a return.

Each section lists one block per tracked player plus a team total.

## Layout note

The game screen scrolls; it isn't pinned to a single viewport. The score call, depth buttons, and
rally buttons all fit above the fold on a phone — the stats panel is below, behind a toggle.
