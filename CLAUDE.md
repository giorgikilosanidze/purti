# Purti (ფურთი) — project notes

A web version of ფურთი, the Georgian card game of elevens, built for fun to play with a friend.

## Files
- `index.html` — the whole app (HTML + CSS + JS, no build step).
- `README.md` — short description and the rules as implemented.

## How it works
- Modes: online with a friend (4-letter room code) and practice vs a computer opponent.
- Online play is peer-to-peer with PeerJS (loaded from unpkg, uses the free PeerJS cloud broker). The host's peer id is `purti-ge-2026-<CODE>`; both sides send the full game state on every change, and the newer state wins (`seq`, then `id`).
- English / Georgian toggle (`T.en` / `T.ka` in the script). Name, language and the last room are kept in localStorage.
- Rules: 4 cards each, 4 on the table; number cards (Ace = 1) take table number cards that add to 11 with them; K takes K, Q takes Q; the Jack takes everything except Q and K; clearing the table gives a ★; leftovers go to the last capturer; most cards wins, ★ breaks ties.

## Next steps
1. Push to the empty repo https://github.com/giorgikilosanidze/purti on `main`.
2. Turn on GitHub Pages (Settings → Pages → Branch: main, / (root)). The site will be at https://giorgikilosanidze.github.io/purti
3. Test online play in two browser tabs (create a game in one, join with the code in the other). This hasn't been tested on a live host yet.
