# Purti (ფურთი) — project notes

A web version of ფურთი, the Georgian card game of elevens, built for fun to play with a friend.

## Files
- `index.html` — the whole app (HTML + CSS + JS, no build step).
- `database.rules.json` — Firebase Realtime Database rules; paste into the console (Realtime Database → Rules).
- `README.md` — description, one-time Firebase setup, and the rules as implemented.

## How it works
- Modes: online (4-letter room code), 2 or 4 players, everyone for themselves; practice is 1 vs the computer only. 4-player games need 4 people (the user chose no computer seats online).
- Online play uses Firebase Realtime Database (compat SDK v10 from gstatic) with anonymous auth. `FIREBASE_CONFIG` near the top of the script must be filled in; while it is `null`, online play shows "not set up".
- Data: `rooms/<CODE>/{created, size, seats, players, state, online}`. `created` is seat 0 and sets `size` (2|4); joiners claim `seats/1..3` in order (RTDB rules have no `numChildren`, so explicit seats cap the room). Seat order is playing order. A name already used in the room is refused. `state` is the whole game as a JSON string (strings avoid Firebase dropping empty arrays). Moves are shown locally at once and saved with a transaction that only accepts a higher `seq`, so the first write wins and everyone follows the server copy. `online/<uid>` is presence via `onDisconnect`.
- Running totals: `results/<group>/<gameId> = {w, t}`, where `<group>` is all players' lowercase names, sorted (`groupKey`), so each pair or foursome has its own record; `w` is "" for a draw. Every client writes the same entry in a transaction; only the first is kept, so each game counts once.
- Players are anonymous Firebase users; the uid is kept per browser, so rejoining works on the same browser/device, not from a different one. `?guest`, `?guest=2`, `?guest=3` give a tab its own uid and storage for testing several seats in one browser.
- Languages: English, Georgian, French (`T.en` / `T.ka` / `T.fr`; `LANGS` sets the switcher: three buttons EN · ქარ · FR, tap one to pick it). Name, language and the last room are kept in localStorage. A name is required for online play.
- Rules: 4 cards each, 4 on the table; number cards (Ace = 1) take table number cards that add to 11 with them; K takes K, Q takes Q; the Jack takes everything except Q and K; leftovers go to the last capturer. Scoring (`scoreRound`): most cards 2, 2♣ 1, 10♦ 1, most clubs 1; a shared lead in cards or clubs earns nobody those points; most points wins, a shared top score is a draw (only possible with 4). No ★ sweeps (the user removed them). No capture hints: tapping a card only selects it (the user wants players to find the 11s themselves).
- Game state is versioned (`STATE_V`, now 2). Bump it when the state shape changes; an older saved game in a room is replaced by a fresh deal.

## Status
- Code is on https://github.com/giorgikilosanidze/purti (`main`).
- Firebase project `purti-73379` (Realtime Database in europe-west1, anonymous auth on, rules published). Its config is in `index.html`.
- Live at https://giorgikilosanidze.github.io/purti (GitHub Pages from `main` / root). Pages caches for ~10 min; add `?v=N` to bypass when testing a fresh deploy.
- Tested 2026-11-12 in two tabs on the live site: create/join, moves (~90 ms tab to tab), refresh + Rejoin mid-game, offline badge, simultaneous "Play again", draw and win recorded once each in totals. Not yet tested on two real devices/networks.
- Test data from that run is in the database (`rooms/*`, `results/testguest~testhost`); the rules don't allow deleting it from the app, so remove it in the Firebase console if wanted.
