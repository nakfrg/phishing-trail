# The Phishing Trail

Survive the corporate work week. Do not click the link.

An educational cybersecurity game in the style of Oregon Trail: five days,
three meters (energy / security / reputation), and a steady stream of phishing
emails, mysterious USB sticks, and people who sound exactly like your CEO but
are not your CEO. Every disaster is based on a real attack; every tip is real
advice.

Originally part of the [nakfrg.com](https://nakfrg.com) arcade, now a
standalone site.

## Structure

- `index.html` — the whole game: styles, pixel renderer, and engine.
- `data/*.json` — all game content (events, openers, scripted beats, filler,
  minigame emails). **Want to add an event? You only need JSON — see
  [CONTRIBUTING.md](CONTRIBUTING.md).**

## Run locally

The game fetches its JSON data, so it needs a web server (opening `index.html`
straight from disk won't work):

```sh
npx http-server . -p 4182 -c-1
# then open http://localhost:4182
```

Any static file server works. Deployment is just: upload the folder.
