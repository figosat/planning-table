# Planning Table

Scrum poker for refinement sessions. Everyone picks a card in private, the team reveals together, and the page shows the average, median, spread and a suggested estimate. Saved estimates build a running log per room.

Static site (GitHub Pages) with a Firebase backend (Firestore + anonymous sign-in), both on free tiers.

## How votes stay hidden

Each player's pick is written to `users/<uid>/picks/<room>`, which only that player can read (see `firestore.rules`). The shared room only records *that* someone voted. When the room is revealed, every open browser copies its own pick to the shared player document. A voter who closed the page before the reveal shows as "still turning over" until they reopen the room.

## One-time Firebase setup

1. Go to <https://console.firebase.google.com>, click **Add project**, give it a name. Google Analytics is not needed.
2. **Build > Authentication > Get started > Sign-in method**: enable **Anonymous**.
3. **Build > Firestore Database > Create database**: pick a region (for example `eur3` or `europe-west1`), start in **production mode**.
4. In Firestore, open the **Rules** tab, replace the content with `firestore.rules` from this repo, then **Publish**.
5. **Project settings (gear) > General > Your apps > Web (`</>`)**: register an app (no Firebase Hosting needed), then copy the `firebaseConfig` values into `config.js`.
6. **Authentication > Settings > Authorized domains**: add `<your-github-username>.github.io`.

The config values in `config.js` are public identifiers, not secrets. Access is controlled by the Firestore rules.

Optional hardening: in Google Cloud console > APIs & Services > Credentials, restrict the browser API key to the HTTP referrer `https://<your-github-username>.github.io/*`.

## Publish on GitHub Pages

In the repo: **Settings > Pages > Build and deployment > Deploy from a branch**, branch `main`, folder `/ (root)`, **Save**. The site appears at `https://<your-github-username>.github.io/planning-table/` within a minute or two.

## Run locally

ES modules need a web server (opening the file directly will not work):

```bash
python3 -m http.server 8080
# then open http://localhost:8080
```

## Free tier limits

Firestore Spark plan: 50,000 reads and 20,000 writes per day, 1 GiB storage. A team of 10 estimating for an hour uses a small fraction of that.

## Privacy note

Anyone with the site URL can sign in anonymously and see the room list and story titles. Don't put confidential information in story titles.
