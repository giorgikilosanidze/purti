# Purti (ფურთი) — project notes

A web version of ფურთი, the Georgian card game of elevens, built for fun to play with a friend.

## Files
- `index.html` — the whole app (HTML + CSS + JS, no build step).
- `database.rules.json` — Firebase Realtime Database rules; paste into the console (Realtime Database → Rules).
- `README.md` — description, one-time Firebase setup, and the rules as implemented.

## How it works
- Modes: online (4-letter room code), 2 or 4 players, everyone for themselves; practice is 1 vs the computer only. 4-player games need 4 people (the user chose no computer seats online).
- Online play uses Firebase Realtime Database (compat SDK v10 from gstatic) with anonymous auth. `FIREBASE_CONFIG` near the top of the script must be filled in; while it is `null`, online play shows "not set up".
- Data: `rooms/<CODE>/{created, size, target, seats, players, state, online}` (`target` 11|21, set by the creator). `created` is seat 0 and sets `size` (2|4); joiners claim `seats/1..3` in order (RTDB rules have no `numChildren`, so explicit seats cap the room). Seat order is playing order. A name already used in the room is refused. `state` is the whole game as a JSON string (strings avoid Firebase dropping empty arrays). Moves are shown locally at once and saved with a transaction that only accepts a higher `seq`, so the first write wins and everyone follows the server copy. `online/<uid>` is presence via `onDisconnect`.
- Running totals count MATCHES (user's choice): `matches/<group>/<matchId> = {w, t}`; the older per-round `results/<group>/<gameId>` is no longer read. Formerly: `results/<group>/<gameId> = {w, t}`, where `<group>` is all players' lowercase names, sorted (`groupKey`), so each pair or foursome has its own record; `w` is "" for a draw. Every client writes the same entry in a transaction; only the first is kept, so each game counts once.
- Players are anonymous Firebase users; the uid is kept per browser, so rejoining works on the same browser/device, not from a different one. `?guest`, `?guest=2`, `?guest=3` give a tab its own uid and storage for testing several seats in one browser.
- Languages: English, Georgian, French (`T.en` / `T.ka` / `T.fr`; `LANGS` sets the switcher: three buttons EN · ქარ · FR, tap one to pick it). Name, language and the last room (`session`, for the Rejoin button) are kept in localStorage. Rejoin is forgotten after Menu on the result screen or the × next to it; leaving mid-round with ← keeps it. A name is required for online play.
- Rules: 4 cards each, 4 on the table; number cards (Ace = 1) take table number cards that add to 11 with them; K takes K, Q takes Q; the Jack takes everything except Q and K; leftovers go to the last capturer. Scoring (`scoreRound`): most cards 2, 2♣ 1, 10♦ 1, most clubs 1; a shared lead in cards or clubs earns nobody those points; most points wins, a shared top score is a draw (only possible with 4). No ★ sweeps (the user removed them). No capture hints (the user wants players to find the 11s themselves). Players CHOOSE the capture (`captureOptions`, `validTake`): tap table cards, then the hand card again; one group per turn; nothing picked = put the card down; an invalid pick is refused with a message. The Jack still takes automatically.
- Matches: `newRound(mode, players, prev, target)` continues `prev`'s match unless `prev.mover`; state has `match` (id), `target`, `tot` (match points), `mover`, `mw`. Match ends when someone reaches the target and is alone on top; a tie at the top plays another round.
- Game state is versioned (`STATE_V`, now 3). Bump it when the state shape changes; an older saved game in a room is replaced by a fresh deal.

## Status
- Code is on https://github.com/giorgikilosanidze/purti (`main`).
- Firebase project `purti-73379` (Realtime Database in europe-west1, anonymous auth on, rules published). Its config is in `index.html`.
- Live at https://giorgikilosanidze.github.io/purti (GitHub Pages from `main` / root). Pages caches for ~10 min; add `?v=N` to bypass when testing a fresh deploy.
- Tested 2026-11-12 in two tabs on the live site: create/join, moves (~90 ms tab to tab), refresh + Rejoin mid-game, offline badge, simultaneous "Play again", draw and win recorded once each in totals. Not yet tested on two real devices/networks.
- Tested 2026-11-13 on the live site with 4 tabs (`?guest=2..4`): 4-player room create/join, taken name refused, seat/turn order, full round to a draw (scores checked by hand), result saved once; 2-player create/join still works. Simulations: 2000 rounds each for 2 and 4 players, scoring checked against an independent tally. Hidden browser tabs throttle timers heavily, so multi-tab tests need the active seat's tab in front.
- Tested 2026-11-13 (matches + chosen captures) on the live site: 11/21 picker, wrong pick refused, chosen group taken exactly, put-down with nothing picked, practice match to 11 ended at 0-15 with New match resetting; online: joiner gets the room's target (21) even with their own pref 11, a chosen capture synced to the other tab, a full online match to 11 (4-1, 4-6, 4-11) saved once in `matches/ana~beka`. Simulations: 1200 matches (2/4 players, to 11/21) with random and illegal picks.
- Test data from that run is in the database (`rooms/*`, `results/testguest~testhost`, `results/ana~beka~cotne~dato`, `matches/ana~beka`); the rules don't allow deleting it from the app, so remove it in the Firebase console if wanted.
