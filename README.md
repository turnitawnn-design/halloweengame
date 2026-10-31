# The Hollow Crown (v2, microsite)

Phone-based version of the Halloween party game. Players join by QR code. The host runs the night from one control screen. The app deals roles and missions, counts curses, runs readings, nominations and tie-breaks, and works out everyone's costume votes.

## Deploy to Vercel (about 5 minutes)

1. **Push to GitHub.** Unzip, then from inside the folder:
   ```
   git init && git add . && git commit -m "Hollow Crown"
   ```
   Create an empty repo on GitHub and push to it (or drag the folder into a new repo with GitHub Desktop).
2. **Import in Vercel.** vercel.com → Add New → Project → pick the repo → Deploy. No settings to change.
3. **Add storage (required).** In the Vercel project: Storage tab → Create / Marketplace → **Upstash for Redis** → free plan → connect it to this project. It adds the env vars automatically (`KV_REST_API_URL` / `KV_REST_API_TOKEN`; `UPSTASH_REDIS_REST_URL` / `UPSTASH_REDIS_REST_TOKEN` also work).
4. **Redeploy** (Deployments → ⋯ → Redeploy) so the new env vars load.
5. Open the site, tap **Create a game**. If you see a red "Storage isn't connected" banner, step 3 or 4 didn't take.

Without Redis the app runs in memory: fine for `npm run dev` on your laptop, broken on Vercel (each request can hit a different server).

## Run locally

```
npm install
npm run dev
```
Open http://localhost:3000. To test with phones on the same Wi-Fi, use your laptop's local IP instead of localhost.

## Running the night

- **Host:** Create a game on your phone. Show the QR code. The "Say this" box tells you what to announce at each step, and the big button moves to the next phase. The host screen never shows roles before the reveal, so the host can play too ("Play too" opens a player view).
- **Cast to a TV:** "Copy private host link" and open it on a laptop. Anyone with that link controls the game, so keep it private.
- **Players:** scan, enter name and costume, then hold the cards to read them. Hold-to-reveal hides everything again the moment they let go.
- **Late arrivals** can still join after the deal. They're in the costume contest only (3 votes), same as the printed rules.

## Rule changes from the print version

- **Slips are automatic.** Everyone taps the same "Seal my slip" button. The server turns each completed Hunter mission into a curse, so nobody can misfile a slip and Monsters and Hunters look identical.
- **Soul readings are app-driven.** Spend your first bonus chip, pick a player, and the app privately shows you their true role. No phone-showing, so no faked screens. You can still lie about what you saw.
- **Flexible player count (5 to 30).** Hunters ≈ 1 in 5 (4 at 20 players, minimum 2 from 10 players). Curse target = number of Hunters (4 at 20, same as print). Suspects stay at 3, and catching 2 Hunters still stops them. Under 10 players it deals 1 Hunter, which is only good for testing.
- Mission pairs repeat only above 20 players.
- Ties at the suspect cutoff and for the crown get one runoff, then lots, as in print. Any lots drawn are shown on screen.

## Costs

Upstash free tier and Vercel Hobby cover a party comfortably. Phones poll every 3 seconds and pause while the screen is off. Games expire from storage after 3 days.

## Structure

- `lib/missions.ts`: all 40 missions, editable
- `lib/game.ts`: rules engine (deal, permissions, tallies, win condition, vote maths)
- `lib/store.ts`: Upstash Redis with in-memory fallback
- `app/play/[code]`: player screen; `app/host/[code]`: host screen; `app/rules`: how to play
