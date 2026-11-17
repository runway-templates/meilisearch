# meilisearch

[Meilisearch](https://www.meilisearch.com) template for Runway. Search engine
with typo tolerance, filtering, faceting, and geosearch built in.

Single instance with persistence on a volume.

> [!TIP]
> Found a problem, or have an idea for this template?
> [Open an issue](https://github.com/runway-templates/schema/issues/new/choose) in the schema repository.
> Issues for all templates are tracked there. Pull requests are welcome right here.

## Deploy

Deploy Meilisearch with one click [from your browser](https://ui.runway.horse/deploy/templates),
or with the Runway CLI:

```sh
runway app create --app=my-meilisearch --plan=scale-m-launch
runway app deploy template --app=my-meilisearch --template=meilisearch
```

To change an [input](#inputs), add it as `key=value` at the end of the second command.

Once the app is up, this prints the connection details for your other apps:

```sh
runway app show --app=my-meilisearch
```

## Inputs

| Input | Default | Notes |
| --- | --- | --- |
| `image_tag` | `v1.53` | The `v` prefix is upstream's convention |
| `environment` | `production` | `development` also serves the search preview UI |
| `no_analytics` | `true` | Telemetry is off; set to `false` to opt back in |

## Outputs

`URL`

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
