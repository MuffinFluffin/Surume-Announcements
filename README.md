# Surume-Announcements

Remote announcement manifest for the Surume iOS app. The app pulls this on cold launch, on foreground return, and once after onboarding:

```
https://raw.githubusercontent.com/MuffinFluffin/Surume-Announcements/main/announcements.json
```

Repo: https://github.com/MuffinFluffin/Surume-Announcements

Each entry shows up either as a notification card (toast) or a fullscreen takeover. Once an entry is dismissed locally, its `id` is remembered and it won't fire again unless you opt into cold-launch repeats or change the `id`.

The schema, parser, and pacing rules are identical across the Surume, Sakura, Surume, and Fin announcement feeds so a card written for one project drops into another with only an id change.

## Schema

```json
{
  "poll": { },
  "announcements": [
    {
      "id": "stable-unique-key",
      "type": "fullscreen",
      "title": "Headline",
      "body": "Markdown body. Inline links like [GitHub](https://github.com/MuffinFluffin/Surume-Announcements) are picked up automatically.",
      "icon": "megaphone.fill",
      "tintKey": "pink",
      "links": [
        { "label": "Open GitHub", "url": "https://github.com/MuffinFluffin/Surume-Announcements", "icon": "arrow.up.right.square", "tintKey": "blue" },
        { "label": "Join Discord", "url": "https://discord.gg/example", "icon": "person.3.fill", "tintKey": "purple" }
      ],
      "url": "https://example.com",
      "minVersion": "1.0.0",
      "maxVersion": "9.0.0",
      "minBuild": 4700,
      "maxBuild": 99999,
      "expiresAt": "2026-12-31T00:00:00Z",
      "showAgainEveryColdLaunch": false,
      "maxColdLaunchDisplays": 40,
      "introSeconds": 3,
      "persistenceSeconds": 8,
      "recurrenceTotalShows": 1,
      "recurrenceIntervalSeconds": 0
    }
  ]
}
```

### Fields

| Field | Required | Notes |
|-------|----------|-------|
| `id` | yes | Stable unique key. Change it if you want everyone to see the entry again. |
| `type` | yes | `card` (toast stack) or `fullscreen`. |
| `title` | yes | Headline. |
| `body` | no | Subtitle / body. Markdown links `[text](url)` render inline and become focusable action buttons. |
| `icon` | no | SF Symbol name. Cards default to `megaphone.fill`. |
| `tintKey` | no | Theme palette key: `pink`, `green`, `red`, `blue`, `purple`, `orange`, `bronze`, `plum`, etc. |
| `links` | no | Array of action buttons rendered under the body. Each entry: `label`, `url`, optional `icon` (SF Symbol), optional `tintKey`. Multiple links are supported. |
| `url` | no | Single legacy link. Auto-merged into `links` as a "Learn More" button when not already present. |
| `minVersion` | no | Hidden if app marketing version `<` this (numeric compare). |
| `maxVersion` | no | Hidden if app marketing version `>=` this. Useful for nagging old builds. |
| `minBuild` | no | Hidden if normalized `CFBundleVersion` `<` this. Leading-digits parser handles values like `4700.1`. |
| `maxBuild` | no | Hidden if normalized build `>` this. |
| `expiresAt` | no | ISO-8601 UTC. Hidden after this instant. |
| `showAgainEveryColdLaunch` | no | Default `false`. When `true`, timed dismissals do not persist to disk and the entry resurfaces on later launches until the user explicitly dismisses it or `maxColdLaunchDisplays` is reached. |
| `maxColdLaunchDisplays` | no | Cap for cold-launch resurfaces. Omit for unlimited. |

### Card timing (`type: "card"` only)

| Field | Default | Notes |
|-------|---------|-------|
| `introSeconds` | `3` | Count-in shown before the persistence timer starts. `0` skips it. |
| `persistenceSeconds` | `8` | Seconds the toast stays on screen after the intro. Forced to `8` when `recurrenceTotalShows > 1` and the value is `<= 0`. |
| `recurrenceTotalShows` | `1` | Total toast appearances including the first. Set `> 1` for spaced repeats. |
| `recurrenceIntervalSeconds` | `0` | Gap between repeats. `0` replays immediately when the prior toast expires. |

Card entries only run when notifications are enabled in the app's OSD or settings panel. Fullscreen entries decode the timing fields but ignore them.

### Inline body links and the action list

The body supports markdown links: `Read about it [on GitHub](https://github.com/MuffinFluffin/Surume-Announcements)`. They render as inline highlighted text on touch and are also collected into the focusable action list under the body so a controller or keyboard can move to each one.

The final action list is built like this, deduped by URL, in order:

1. Every entry in `links`.
2. Every `[label](url)` parsed from the body.
3. The legacy `url` as "Learn More" (only if not already covered).

A "Dismiss" row is always appended at the end.

### Controller and keyboard navigation

Fullscreen entries are fully navigable without touch:

- D-pad up / down or arrow keys move focus through the action rows.
- A / Cross / Enter opens the focused link, then dismisses.
- B / Circle / Escape dismisses without opening anything.
- Background tap also dismisses.

The hint footer at the bottom shows the right glyphs (Xbox-style or PlayStation-style) based on which controller is connected.

### Fetch pacing (root `poll` object)

| Field | Default | Notes |
|-------|---------|-------|
| `maxSuccessfulFetchesPerRollingMinute` | `2` | Counts decoded HTTP 200s only. Rolling 60 s window. |
| `maxSuccessfulFetchesPerRollingHour` | `12` | Rolling 1 hour. |
| `maxSuccessfulFetchesPerRollingDay` | `48` | Rolling 24 hours. |
| `minSpacingSecondsBetweenAttempts` | `90` | Minimum gap between outbound fetch attempts. Floored at 15 s. |
| `emergencyRelaxedUntilUTC` | none | ISO-8601 UTC. While the device clock is before this instant, rolling success caps double. Min spacing still applies. |

The latest manifest that contained `poll` overwrites the persisted hints on device. Omitting `poll` keeps whatever hints were already saved.

## Examples

### Multi-link fullscreen

```json
{
  "id": "release-notes-2026-05",
  "type": "fullscreen",
  "title": "What's new in Surume",
  "body": "Refreshed touch controls, controller navigation, neural upscale on iOS. Read the full notes on [GitHub](https://github.com/MuffinFluffin/Surume-Announcements).",
  "icon": "sparkles",
  "tintKey": "pink",
  "links": [
    { "label": "Release notes", "url": "https://github.com/MuffinFluffin/Surume-Announcements/releases", "icon": "doc.text.fill" },
    { "label": "Discord", "url": "https://discord.gg/example", "icon": "bubble.left.and.bubble.right.fill", "tintKey": "purple" }
  ],
  "expiresAt": "2026-08-01T00:00:00Z"
}
```

### Spaced toast nag for outdated builds

```json
{
  "id": "upgrade-2026-05",
  "type": "card",
  "title": "Update available",
  "body": "Open the App Store to grab the latest build.",
  "icon": "arrow.down.circle.fill",
  "tintKey": "red",
  "maxVersion": "2.99.99",
  "expiresAt": "2026-12-31T23:59:59Z",
  "introSeconds": 3,
  "persistenceSeconds": 12,
  "recurrenceTotalShows": 12,
  "recurrenceIntervalSeconds": 300
}
```

## Publishing

1. Edit `announcements.json` in this repo.
2. Merge to `main`. GitHub serves the raw CDN within a minute.
3. Clients pull on next cold launch, foreground return, or post-onboarding completion.

Manifest-only changes do not need an App Store rebuild. Only changes to the fetch URL or the parser require a new app binary.
