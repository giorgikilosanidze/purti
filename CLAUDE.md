# Purti (ფურთი) — project notes

A web version of ფურთი, the Georgian card game of elevens, built for fun to play with a friend.

## Files
- `index.html` — the whole app (HTML + CSS + JS, no build step).
- `database.rules.json` — Firebase Realtime Database rules; paste into the console (Realtime Database → Rules).
- `README.md` — description, one-time Firebase setup, and the rules as implemented.

## How it works
- Modes: online with a friend (4-letter room code) and practice vs a computer opponent.
- Online play uses Firebase Realtime Database (compat SDK v10 from gstatic) with anonymous auth. `FIREBASE_CONFIG` near the top of the script must be filled in; while it is `null`, online play shows "not set up".
- Data: `rooms/<CODE>/{created, guest, players, state, online}`; `created` and `guest` hold the two seats' uids (RTDB rules have no `numChildren`, so seats cap the room at two). `state` is the whole game as a JSON string (strings avoid Firebase dropping empty arrays). Moves are shown locally at once and saved with a transaction that only accepts a higher `seq`, so the first write wins and everyone follows the server copy. `online/<uid>` is presence via `onDisconnect`.
- Running totals: `results/<pair>/<gameId> = {w, t}`, where `<pair>` is the two lowercase names, sorted. Both clients write the same entry; the rules only accept the first, so each game counts once. Totals are counted client-side.
- Players are anonymous Firebase users; the uid is kept per browser, so rejoining works on the same browser/device, not from a different one. `?guest` in the URL gives a tab its own uid for testing.
- English / Georgian toggle (`T.en` / `T.ka` in the script). Name, language and the last room are kept in localStorage. A name is required for online play.
- Rules: 4 cards each, 4 on the table; number cards (Ace = 1) take table number cards that add to 11 with them; K takes K, Q takes Q; the Jack takes everything except Q and K; clearing the table gives a ★; leftovers go to the last capturer; most cards wins, ★ breaks ties.

## Status
- Code is on https://github.com/giorgikilosanidze/purti (`main`).
- Firebase project `purti-73379` (Realtime Database in europe-west1, anonymous auth on, rules published). Its config is in `index.html`.
- To do: turn on GitHub Pages, then test online play in two tabs (one with `?guest`).
