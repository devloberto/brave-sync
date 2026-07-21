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

This produces two locally tagged images:

- `brave-sync-web`
- `brave-sync-dynamo`

On every push to `master` (and on `v*.*.*` tags) the same images are built
and published to GitHub Container Registry as:

- `ghcr.io/<owner>/<repo>-web`
- `ghcr.io/<owner>/<repo>-dynamo`

see [`.github/workflows/docker-publish.yml`](.github/workflows/docker-publish.yml).

## use the self-hosted sync server

Go to brave://flags/#brave-override-sync-server-url and enter your URL.
