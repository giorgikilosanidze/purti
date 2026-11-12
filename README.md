# ფურთი — Purti

The Georgian card game of elevens, as a small web app. Play online with a friend using a 4-letter code, or practise against the computer.

- One file: `index.html` (no build step).
- Online play is peer-to-peer through [PeerJS](https://peerjs.com/): the two browsers connect directly, so there is no server to run.
- Works on phones, tablets and desktop. English and Georgian.

## Run it

Open `index.html` in a browser, or host the folder on GitHub Pages: **Settings → Pages → Deploy from a branch → `main` / root**.

## Rules (as implemented)

- Two players, 52 cards. 4 cards each, 4 face-up on the table; 4 more each whenever both hands are empty.
- A number card (Ace = 1) takes table number cards that add up to 11 with it.
- King takes a King, Queen takes a Queen.
- The Jack (the ფურთი) takes everything except Queens and Kings.
- Clearing the table earns a ★. Leftovers go to whoever took last. Most cards wins; ★ breaks ties.
