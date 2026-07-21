<p align="center"><img src="https://avatars.githubusercontent.com/u/12301619?s=200" width="120"></p>

# brave sync server

This project builds the docker images needed to self-host the
[brave sync server](https://github.com/brave/go-sync).

## build docker images

The images are built from [brave/go-sync](https://github.com/brave/go-sync)
using [Task](https://taskfile.dev):

```sh
task build
```

This produces two locally tagged images (amd64 by default):

- `brave-sync-web`
- `brave-sync-dynamo`

On every push to `master` (and on `v*.*.*` tags) the same images are built
and published (amd64 and arm64) to GitHub Container Registry as:

- `ghcr.io/<owner>/<repo>-web`
- `ghcr.io/<owner>/<repo>-dynamo`

See [`.github/workflows/docker-publish.yml`](.github/workflows/docker-publish.yml).

## use the self-hosted sync server

Pull the two published images:

```sh
docker pull ghcr.io/devloberto/brave-sync-web:latest
docker pull ghcr.io/devloberto/brave-sync-dynamo:latest
```

Then start them together with a redis instance — the simplest way
is to use the [compose.yml](./compose.yml) that I use for development
purpose and which is based on the `docker-compose.yml` from
the [brave/go-sync](https://github.com/brave/go-sync) repository.

Once the server is running, point Brave at it. The current go-sync server
only exposes the v2 sync API at `/v2/command/`, so the legacy
`brave://flags/#brave-override-sync-server-url` flag will **not** work
(requests to `/command/` will return 404). That flag also strips any
path suffix, so the workaround is to set the bare host URL and let the
included [Caddy](./Caddyfile) reverse proxy rewrite `/command/` to
`/v2/command/` for you.

Visit [brave://flags/#brave-override-sync-server-url](brave://flags/#brave-override-sync-server-url),
enable the flag, and enter your server's base URL (no path):

```
http://<your-host>:8295
```

And do not forget to enable Sync at [brave://settings/braveSync](brave://settings/braveSync).

### Verify sync status

Visit [brave://sync-internals](brave://sync-internals).

## Credits

- [https://github.com/brave/go-sync](https://github.com/brave/go-sync)
- [https://github.com/tborychowski/self-hosted-cookbook/blob/master/apps/other/brave-sync.md](https://github.com/tborychowski/self-hosted-cookbook/blob/master/apps/other/brave-sync.md)
