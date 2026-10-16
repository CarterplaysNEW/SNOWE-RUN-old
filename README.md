# Cartblox

A browser-based "classic brick" game site: home page, Catalog, CartStudio (beta), and multiplayer games.

```
index.html            the website (GitHub Pages serves this)
config.js             YOUR settings: Render server URL + Firebase config   <-- edit this
Dockerfile            builds the multiplayer server on Render             <-- must be named exactly "Dockerfile"
server/server.js      the multiplayer server (Node + WebSocket)
server/package.json
render.yaml           optional Render blueprint
database.rules.json   Firebase Realtime Database rules to paste in the console
.dockerignore / .nojekyll
```

## 1. The website (GitHub Pages)
1. Put all of these files in your GitHub repo (keep the folder layout above, `Dockerfile` in the repo root).
2. Repo **Settings > Pages**: deploy from the `main` branch, root folder.
3. Your site is at `https://YOUR-NAME.github.io/YOUR-REPO/`. CartStudio opens at `.../#/CartStudio`.

## 2. The game server (Render + Docker)
1. Render dashboard > **New > Web Service** > pick this GitHub repo.
2. **Language / Runtime: Docker**. Leave *Root Directory* empty and *Dockerfile Path* as `./Dockerfile`.
3. Pick a plan (Free works) and create the service. No environment variables or keys are needed.
   (Optional: `MAX_PLAYERS`, default 16 per server instance.)
4. When it says *Live*, copy the service URL (like `https://cartblox-server.onrender.com`).
5. Open `config.js` and paste it into `serverUrl`, then commit.

Test it: open `https://YOUR-SERVICE.onrender.com/health` (you should see `{"ok":true}`) and `/stats`.
The free plan sleeps when idle, so the first player after a while waits up to a minute while it wakes up. The game tells them, and they can play solo meanwhile.

## 3. Firebase (chat, home feed, shared catalog items)
`config.js` already contains your Firebase web config.
1. Firebase console > **Build > Realtime Database > Create database** (if you have not already).
2. Open the **Rules** tab, paste the contents of `database.rules.json`, and **Publish**.
   These rules let anyone read, and only let people *add* short, well-formed messages/items (nobody can edit or delete).

## How the pieces talk
- Players' positions go through the **Render** WebSocket server (about 15 updates per second).
- Chat messages, the home feed and community catalog items go through **Firebase**.
- If either service is missing or unreachable, the site keeps working (single-player / this-browser-only).

## Local testing
```
cd server && npm install && npm start      # server on :10000
# then open index.html with ?server=http://localhost:10000
```
