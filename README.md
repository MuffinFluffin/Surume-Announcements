# Surume-Announcements

Live announcements for the [Surume](https://github.com/MuffinFluffin/Surume) PlayStation 2 emulator for iOS.

## How it works

The app fetches `announcements.json` from this repo on every launch (when on the Library or Settings screen). Announcements can appear as notification cards or full-screen pop-ups.

## announcements.json format

```json
{
  "announcements": [
    {
      "id": "unique-string",
      "type": "card | fullscreen",
      "title": "Title text",
      "body": "Body / subtitle text",
      "icon": "SF Symbol name (optional, defaults to megaphone.fill)",
      "tintKey": "color key (optional)",
      "url": "optional deep link or web URL",
      "minVersion": "1.0.0 (optional — show only if app version >= this)",
      "maxVersion": "2.0.0 (optional — show only if app version < this)",
      "expiresAt": "ISO-8601 date string (optional)"
    }
  ]
}
```

### Fields

| Field | Required | Description |
|-------|----------|-------------|
| `id` | Yes | Unique identifier. Once dismissed, won't show again. |
| `type` | Yes | `"card"` for notification toast, `"fullscreen"` for modal. |
| `title` | Yes | Announcement title. |
| `body` | No | Subtitle / description. |
| `icon` | No | SF Symbol name. Defaults to `megaphone.fill`. |
| `tintKey` | No | Color swatch key from the Surume theme palette. |
| `url` | No | URL opened when user taps "Learn More" (fullscreen only). |
| `minVersion` | No | Only show if app version >= this. |
| `maxVersion` | No | Only show if app version < this. |
| `expiresAt` | No | ISO-8601 date. Announcement auto-expires after this. |

## Adding an announcement

1. Edit `announcements.json`.
2. Add a new entry with a unique `id`.
3. Commit and push. Users will see it on next app launch.

## License

GPL-3.0+
