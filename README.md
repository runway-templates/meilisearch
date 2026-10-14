# meilisearch

[Meilisearch](https://www.meilisearch.com) template for Runway. Search engine
with typo tolerance, filtering, faceting, and geosearch built in.

Single instance with persistence on a volume.

## Deploy

WIP

## Inputs

| Input | Default | Notes |
| --- | --- | --- |
| `image_tag` | `v1.53` | The `v` prefix is upstream's convention |
| `environment` | `production` | `development` also serves the search preview UI |
| `no_analytics` | `true` | Telemetry is off; set to `false` to opt back in |

## Connecting

This one **is** routed, because search is called from browsers and other
apps. It gets a public HTTPS URL:

```
https://<app>.pqapp.dev
```

`<app>` is your app name. Every request needs an API key.

The master key is in the app config as `MEILI_MASTER_KEY`:

```sh
runway app config ls --app <app>
```

Use it to read the keys Meilisearch derives from it:

```sh
curl -H "Authorization: Bearer <MEILI_MASTER_KEY>" https://<app>.pqapp.dev/keys
```

That returns a default search key and a default admin key. Ship the **search**
key to clients.

## Notes

- **Never ship the master key to a browser or a mobile app.** It can do
  anything, including deleting every index. Hand out the derived search key.
- **Changing the master key invalidates every derived key**, so all clients
  have to be re-keyed. It cannot be recovered from the database.
- `MEILI_HTTP_ADDR` binds `0.0.0.0` on purpose — the upstream default is
  loopback, which neither the health probe nor the route can reach.
- Indexes, dumps, and snapshots all live under the volume, so they survive
  restarts.
- The index format is tied to the version that wrote it. Downgrades need a
  dump and restore.

## License

Meilisearch is MIT. This template is maintained by Runway.
