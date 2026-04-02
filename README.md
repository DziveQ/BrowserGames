# Tempo — How Good Is Your Timing?

A rhythm memory game. We play five patterns, you tap them back.
Solo, multiplayer lobbies, and daily challenge.

## Deploy to Vercel (easiest)

1. Push this repo to GitHub
2. Go to [vercel.com](https://vercel.com) → Import → Select the repo
3. Click Deploy — done

## Deploy to GitHub Pages

1. Push to GitHub
2. Settings → Pages → Source: `main` branch, folder: `/public`
3. Save — your site is live at `https://yourusername.github.io/tempo/`

## Enable Multiplayer (Firebase)

Without Firebase, Solo mode works fully. Multiplayer & Daily work locally but scores won't sync online.

To enable online multiplayer:

1. Go to [Firebase Console](https://console.firebase.google.com)
2. Create a new project (free tier is enough)
3. Build → **Realtime Database** → Create Database → **Start in test mode**
4. Project Settings → Your apps → **Web** → Register app → Copy the config
5. Open `public/index.html` and paste your config into `FIREBASE_CONFIG`:

```js
const FIREBASE_CONFIG = {
  apiKey: "AIzaSy...",
  authDomain: "your-project.firebaseapp.com",
  databaseURL: "https://your-project-default-rtdb.firebaseio.com",
  projectId: "your-project",
  storageBucket: "your-project.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

6. Redeploy

### Firebase Security Rules (production)

For production, update your Realtime Database rules:

```json
{
  "rules": {
    "lobbies": {
      "$lobbyId": {
        "players": {
          "$playerId": {
            ".read": true,
            ".write": true
          }
        }
      }
    },
    "daily": {
      "$date": {
        "players": {
          "$playerId": {
            ".read": true,
            ".write": true
          }
        }
      }
    }
  }
}
```

## How It Works

- **Solo**: Random patterns, play as many times as you want
- **Multiplayer**: Lobby code seeds the RNG, so everyone gets the exact same patterns. Share the link, compete on timing.
- **Daily**: Date-based seed = same patterns for everyone worldwide, once per day

Scoring uses a dual-Gaussian curve on timing ratios (similar to dialed.gg's approach), rewarding near-perfect accuracy while giving partial credit.

## Stack

- Single HTML file, zero dependencies (besides Google Fonts + Firebase SDK)
- Firebase Realtime Database for multiplayer sync (optional)
- Seeded PRNG (Mulberry32) for deterministic patterns
- Web Audio API for beat sounds
- Pure CSS animations
