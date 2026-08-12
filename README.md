<h1 align="center">Valerie — shared About data</h1>

<p align="center">
  One place for the name, the links and the avatar that every Valerie app shows on its About page.
</p>

---

## Why this exists

The same author card was copied into every application: the same avatar file, the same links, typed again each time. Two things follow from that, and both had already happened by the time this repo was made:

- The **avatar was duplicated four times**, 3.2 MB each, and every copy could drift from the others.
- The **CivitAI link disagreed with itself** — `/user/valerie_4659` in two apps, `/user/Valerie` in a third. One of them was wrong, and nobody could tell which without checking.

Changing a link meant editing four repositories and shipping four releases. Now it means editing one file here.

## The contract

`about.json` is the whole interface. Applications read it from:

```
https://raw.githubusercontent.com/valerie-4659/valerie-about/main/about.json
```

```jsonc
{
  "version": 1,              // bumped only for a breaking change
  "updated": "2026-08-12",   // informational
  "name": "Valerie",
  "tagline": "…",            // one short line, shown under the name
  "avatar": "https://…/avatar.png",
  "links":  [ { "label": "CivitAI", "url": "https://…" } ],
  "apps":   [ { "name": "…", "summary": "…", "url": "https://…" } ]
}
```

`links` is where someone finds the author. `apps` is the other tools, so each application can point at its siblings and the list stays current when a new one appears.

## Rules for changing it

- **Additive changes only.** A new field is free; renaming or removing one breaks every installed copy of every app, including the ones nobody is going to update. If a breaking change is genuinely needed, raise `version` and leave the old shape readable.
- `label` and `name` are display text. They can change freely.
- The avatar is replaced in place, at the same path. Keep it square-ish and under about half a megabyte — it is fetched by every app on every machine.

## How applications consume it

Following the same discipline as the update check in these apps:

1. **Bundle a copy** of `about.json` and the avatar with the application. It must render its About page with no network at all — these are offline-first tools, and a blank card on a train is a bug.
2. **Fetch in the background**, from the main process, at most once a day. Cache the result next to the app's own data.
3. **Fail silently.** Offline, rate-limited, malformed JSON — all mean "keep showing what you already have". None of them is worth a message to the user.
4. **Validate before use.** Check `version`, and that `links` is an array of objects with a `label` and an `http(s)` URL. A field that is not what it should be is skipped, not rendered.
5. Open links **in the system browser**, never in an application window.

## What does not belong here

Anything about one particular app: its blurb, its feature list, its changelog. Those live with the app. This file is the person, not the product.

No email address, no anything that would have to be scrubbed later — this repository is public and permanent.
