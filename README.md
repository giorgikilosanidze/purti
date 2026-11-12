# ფურთი — Purti

The Georgian card game of elevens, as a small web app. Play online with 1 or 3 friends using a 4-letter code (2- or 4-player games), or practise against the computer.

- One file: `index.html` (no build step).
- Online play goes through [Firebase Realtime Database](https://firebase.google.com/docs/database). The game lives on the server, so a refresh, a dropped connection or a closed tab doesn't lose it: open the page again and tap "Rejoin".
- Games are matches to 11 or 21 points (you pick on the home screen; joiners get the room's choice).
- All-time match wins are saved for each group of players (a pair or a foursome) and shown during the game and after each round. They're kept under your names, so use the same names every time; two players in one game can't share a name.
- Works on phones, tablets and desktop. English, Georgian and French.

## One-time Firebase setup

1. Go to https://console.firebase.google.com, **Create a project** (Google Analytics isn't needed).
2. **Build → Authentication → Get started → Sign-in method → Anonymous → Enable.**
3. In Authentication → **Settings → Authorized domains**, add `giorgikilosanidze.github.io`.
4. **Build → Realtime Database → Create database**, pick a location, start in **locked mode**.
5. In Realtime Database → **Rules**, replace everything with the contents of `database.rules.json` and click **Publish**.
6. **Project settings (⚙) → Your apps → Web (`</>`)**, register an app (no Hosting needed), and copy the `firebaseConfig` object. Make sure it includes `databaseURL`.
7. In `index.html`, replace `const FIREBASE_CONFIG = null;` with that object, e.g. `const FIREBASE_CONFIG = { apiKey: "...", authDomain: "...", databaseURL: "...", projectId: "...", appId: "..." };`

The config values are not secret; the rules decide who can read and write. Only the players seated in a room can see or change it.

The free Spark plan is far more than a few friends need.

## Run it

Host the folder on GitHub Pages: **Settings → Pages → Deploy from a branch → `main` / root**. Practice mode also works by opening `index.html` directly.

To try online play alone, open the site in several tabs and add `?guest`, `?guest=2`, `?guest=3` to the extra ones; each plays as a separate player.

## Rules (as implemented)

- 2 or 4 players, each for themselves, 52 cards. 4 cards each, 4 face-up on the table; 4 more each whenever all hands are empty. Turns go round in seat order; the first player moves one seat each round.
- A number card (Ace = 1) can take table number cards that add up to 11 with it. The player chooses which: tap the table cards, then tap your card again. One group per turn (with a 3 and 8, 4, 4 on the table: the 8 or 4+4, not both). Pick nothing and the card is just put down; a pick that doesn't work is refused.
- King takes a King, Queen takes a Queen (you pick which one if there are several).
- The Jack (the ფურთი) takes everything except Queens and Kings, automatically.
- At the end, cards left on the table go to whoever took last.
- Points, 5 per round: most cards 2, the 2♣ 1, the 10♦ 1, most clubs 1. If the lead in cards or clubs is shared, nobody gets those points. Most points wins the round; with 2 players a draw is impossible, with 4 a shared top score is a draw.
- No hints: the game never shows what a card could take; you work out the 11s yourself.
- Round points add up. The first to reach 11 (or 21) wins the match; if several reach it in the same round, the highest total wins, and a tie at the top plays another round.
