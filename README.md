# soccer-api

![CI](https://github.com/LeSingh1/soccer-api/actions/workflows/test.yml/badge.svg) ![license](https://img.shields.io/badge/license-MIT-blue.svg) ![node](https://img.shields.io/badge/node-%E2%89%A518-brightgreen.svg) ![dependencies](https://img.shields.io/badge/dependencies-0-success.svg)

Unofficial Node client for ESPN's soccer (Premier League) data. ESPN serves soccer (Premier League) under
`soccer/eng.1`, and this wraps those endpoints with retry/timeout
transport and parsers that return clean data.

Part of the [espn-api](https://github.com/LeSingh1/espn-api) family. The parent
is one client for every sport. This one is just soccer (Premier League), in the style of
swar/nba_api.

## Run it

No dependencies. Node 18+ (built-in fetch).

```
git clone https://github.com/LeSingh1/soccer-api
cd soccer-api
node examples/scoreboard.js   # soccer (Premier League) games
node examples/gamelog.js      # a real player's clean game log
npm test
```

## Use it

```js
import { scoreboardClean, gamelogClean } from "./src/api.js";

await scoreboardClean({ dates: "20260906" });   // tidy array of games
await gamelogClean("ATHLETE_ID");               // [{ date, opponent, atVs, stats }]
```

Raw endpoints: `scoreboard`, `teams`, `team`, `roster`, `athlete`, `athletes`, `gamelog`, `splits`, `news`, `summary`, `standings`, `odds`, `plays`. Parsed helpers that hand back clean data: `scoreboardClean`, `teamsClean`, `rosterClean`, `gamelogClean`.

## Daily data

A scheduled GitHub Action runs once a day, pulls that day's soccer (Premier League) games from
live ESPN, and commits them to `data/latest.json` when there are any. Off-season
days leave the last snapshot in place, so the file is always the most recent day
that actually had games. The same run is a canary: if ESPN changes or breaks an
endpoint the job fails, so a red build is the early warning that the client needs
a fix. Run it yourself with `npm run snapshot:daily`.

## Transport

12 second timeout, up to 3 retries on 429 and 5xx with backoff and Retry-After.
Failures throw an Error whose `.status` and `.url` are set, so you can branch on
the HTTP status (e.g. skip a 404, back off on a 429).

## Honest notes

- Unofficial. ESPN does not document or support these endpoints, so they can
  change or break without warning. Cache responses and do not hammer it.
- Endpoint map credit: [this community gist](https://gist.github.com/nntrn/ee26cb2a0716de0947a0a4e9a157bc1c).

## License

MIT.
