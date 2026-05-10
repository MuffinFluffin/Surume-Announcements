# Surume-Announcements

Remote announcement manifest for the Surume iOS app. Pulled on cold launch and on foreground return:

```
https://raw.githubusercontent.com/MuffinFluffin/Surume-Announcements/main/announcements.json
```

Each entry shows up either as a notification card or a fullscreen sheet. Once dismissed, the `id` is remembered and won't fire again unless you change it or opt into cold-launch repeats.

## Schema

```json
{
  "poll": { },
  "announcements": [
    {
      "id": "stable-unique-key",
      "type": "fullscreen",
      "title": "Headline",
      "body": "Body. Inline links like [GitHub](https://github.com/MuffinFluffin/Surume-Announcements) work.",
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
| `id` | yes | Stable unique key. Change it to re-show. |
| `type` | yes | `card` or `fullscreen`. |
| `title` | yes | Headline. |
| `body` | no | Body text. Markdown `[label](url)` links work. |
| `icon` | no | SF Symbol. Defaults to `megaphone.fill`. |
| `tintKey` | no | Theme palette key (`pink`, `green`, `red`, `blue`, `purple`, etc.). |
| `links` | no | Array of `{label, url, icon?, tintKey?}` action buttons. |
| `url` | no | Single legacy link, merged into `links` as "Learn More". |
| `minVersion` | no | Hidden if app version `<` this. |
| `maxVersion` | no | Hidden if app version `>=` this. |
| `minBuild` | no | Hidden if `CFBundleVersion` `<` this. |
| `maxBuild` | no | Hidden if `CFBundleVersion` `>` this. |
| `expiresAt` | no | ISO-8601 UTC. Hidden after this. |
| `showAgainEveryColdLaunch` | no | When `true`, timed dismissals don't persist until the user explicitly dismisses or the cap is hit. |
| `maxColdLaunchDisplays` | no | Cap for cold-launch repeats. |

### Card timing (`type: "card"` only)

| Field | Default | Notes |
|-------|---------|-------|
| `introSeconds` | `3` | Count-in before persistence. `0` skips. |
| `persistenceSeconds` | `8` | Time on screen after intro. |
| `recurrenceTotalShows` | `1` | Total appearances. `> 1` enables spaced repeats. |
| `recurrenceIntervalSeconds` | `0` | Gap between repeats. |

Card entries only run when notifications are enabled in the app. Fullscreen ignores timer fields.

### Fetch pacing (`poll`)

| Field | Default |
|-------|---------|
| `maxSuccessfulFetchesPerRollingMinute` | `2` |
| `maxSuccessfulFetchesPerRollingHour` | `12` |
| `maxSuccessfulFetchesPerRollingDay` | `48` |
| `minSpacingSecondsBetweenAttempts` | `90` |
| `emergencyRelaxedUntilUTC` | none |

Latest manifest with `poll` overwrites stored hints. Omitting `poll` keeps prior hints.

## Examples

```json
{
  "id": "release-notes-2026-05",
  "type": "fullscreen",
  "title": "What's new in Surume",
  "body": "Read the full notes on [GitHub](https://github.com/MuffinFluffin/Surume-Announcements).",
  "icon": "sparkles",
  "tintKey": "pink",
  "links": [
    { "label": "Release notes", "url": "https://github.com/MuffinFluffin/Surume-Announcements/releases", "icon": "doc.text.fill" },
    { "label": "Discord", "url": "https://discord.gg/example", "icon": "bubble.left.and.bubble.right.fill", "tintKey": "purple" }
  ],
  "expiresAt": "2026-08-01T00:00:00Z"
}
```

```json
{
  "id": "upgrade-2026-05",
  "type": "card",
  "title": "Update available",
  "body": "Open the App Store for the latest build.",
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

Edit `announcements.json` on `main`, merge. The raw CDN updates within a minute. No App Store rebuild needed for manifest-only edits.
