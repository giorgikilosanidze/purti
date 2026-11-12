# ფურთი — Purti

The Georgian card game of elevens, as a small web app. Play online with a friend using a 4-letter code, or practise against the computer.

- One file: `index.html` (no build step).
- Online play goes through [Firebase Realtime Database](https://firebase.google.com/docs/database). The game lives on the server, so a refresh, a dropped connection or a closed tab doesn't lose it: open the page again and tap "Rejoin".
- All-time results between the two of you are saved and shown during the game and after each round. They're kept under your two names, so use the same names every time.
- Works on phones, tablets and desktop. English, Georgian and French.

## One-time Firebase setup

1. Go to https://console.firebase.google.com, **Create a project** (Google Analytics isn't needed).
2. **Build → Authentication → Get started → Sign-in method → Anonymous → Enable.**
3. In Authentication → **Settings → Authorized domains**, add `giorgikilosanidze.github.io`.
4. **Build → Realtime Database → Create database**, pick a location, start in **locked mode**.
5. In Realtime Database → **Rules**, replace everything with the contents of `database.rules.json` and click **Publish**.
6. **Project settings (⚙) → Your apps → Web (`</>`)**, register an app (no Hosting needed), and copy the `firebaseConfig` object. Make sure it includes `databaseURL`.
7. In `index.html`, replace `const FIREBASE_CONFIG = null;` with that object, e.g. `const FIREBASE_CONFIG = { apiKey: "...", authDomain: "...", databaseURL: "...", projectId: "...", appId: "..." };`

The config values are not secret; the rules decide who can read and write. Only the two players in a room can see or change it.

The free Spark plan is far more than two players need.

## Run it

Host the folder on GitHub Pages: **Settings → Pages → Deploy from a branch → `main` / root**. Practice mode also works by opening `index.html` directly.

To try online play alone, open the site in two tabs and add `?guest` to one of the URLs; that tab plays as a separate player.

## Rules (as implemented)

- Two players, 52 cards. 4 cards each, 4 face-up on the table; 4 more each whenever both hands are empty.
- A number card (Ace = 1) takes table number cards that add up to 11 with it.
- King takes a King, Queen takes a Queen.
- The Jack (the ფურთი) takes everything except Queens and Kings.
- At the end, cards left on the table go to whoever took last.
- Points, 5 per round: most cards 2 (nobody gets them on a 26–26 tie), the 2♣ 1, the 10♦ 1, most clubs 1. Most points wins the round; a draw is impossible.
- No hints: tapping a card only picks it; you work out the 11s yourself. Tap it again to play it.
