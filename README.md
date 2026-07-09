# theboxingtimer.com

Public website and remote configuration for the Boxing Timer iOS app, served by GitHub Pages at https://theboxingtimer.com.

Every push to `main` deploys automatically. There is no build step - these files are served exactly as committed.

## Layout

```
index.html          landing page
privacy/            privacy policy (linked from the App Store listing)
support/            support page + FAQ (linked from the App Store listing)
sponsor/            sponsorship pitch page
config/sponsor.json the sponsor card every installed app fetches
assets/             shared stylesheet, sponsor logo images
CNAME               custom domain binding for GitHub Pages
```

## Changing the sponsor card (the important part)

The app in every user's pocket fetches `config/sponsor.json` at most once per day. To change what the sponsor slot shows worldwide: edit that file, commit, push. No app update, no App Store review. Apps pick it up within ~24 hours of the push.

A live sponsor looks like:

```json
{
  "id": "acme-2026-q4",
  "name": "Acme Boxing Co.",
  "tagline": "20% off wraps and gloves",
  "imageURLs": {
    "2x": "https://theboxingtimer.com/assets/sponsors/acme@2x.png",
    "3x": "https://theboxingtimer.com/assets/sponsors/acme@3x.png"
  },
  "linkURL": "https://acmeboxing.com/?ref=boxingtimer",
  "promoCode": "TIMER20",
  "startDate": "2026-10-01T00:00:00Z",
  "endDate": "2026-12-31T23:59:59Z"
}
```

Field reference:

- `id` (required) - unique per campaign; changing it is what marks a new campaign
- `name` (required) - sponsor name shown on the card
- `tagline` (required) - one short line under the name
- `imageURLs` (optional) - logo PNGs keyed by scale (`1x`/`2x`/`3x`); put the files in `assets/sponsors/`. Without a logo the card shows a monogram of the name
- `linkURL` (optional) - where tapping the card goes
- `promoCode` (optional) - displayed on the card
- `startDate` / `endDate` (optional, ISO 8601) - outside this window the app ignores the config and shows its built-in house card, so an expired campaign degrades safely on its own

Rules that keep it safe:

- Keep the JSON strictly valid - a malformed file is ignored by the app (it falls back to the house card), so a typo can't break anything, but it does silently un-publish your campaign. Validate before pushing: `python3 -m json.tool config/sponsor.json`
- Between paid campaigns, restore the house card (the current committed state) rather than leaving an expired sponsor in place
- Git history doubles as the campaign ledger: one commit per sponsor change, message like `sponsor: run acme q4 campaign`

## DNS

`theboxingtimer.com` points at GitHub Pages with these records:

```
A     @    185.199.108.153
A     @    185.199.109.153
A     @    185.199.110.153
A     @    185.199.111.153
CNAME www  slkdjfhihidd.github.io.
```

If hosting ever moves, keep the URLs identical - `config/sponsor.json` is compiled into shipped apps.
