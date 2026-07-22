# The Phishing Trail

Survive the corporate work week. Do not click the link.

## Structure

- `index.html` — the whole game: styles, pixel renderer, and engine.
- `data/*.json` — all game content (events, openers, scripted beats, filler,
  minigame emails). **Want to add an event? You only need JSON — see
  [CONTRIBUTING.md](CONTRIBUTING.md).**

## Run locally

The game fetches its JSON data, so it needs a web server

```sh
npx http-server . -p 4182 -c-1
# then open http://localhost:4182
```

Any static file server works. Deployment is just: upload the folder.
