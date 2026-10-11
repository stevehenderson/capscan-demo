# capscan-demo

A working demonstration of [capscan](https://github.com/stevehenderson/capscan), a companion to Dependabot that reviews each dependency update for new capabilities a repository could adopt.
It accompanies the blog post [Your Dependencies Shipped Features Too](https://stevehenderson.github.io/Dependencies-Shipped-Features/) and its [Go appendix](https://stevehenderson.github.io/Dependencies-Shipped-Features/#go-appendix).

Look at the [pull requests](https://github.com/stevehenderson/capscan-demo/pulls?q=is%3Apr+author%3Aapp%2Fdependabot) for capscan's comment on each Dependabot update, and the [issues](https://github.com/stevehenderson/capscan-demo/issues?q=is%3Aissue+capscan) for its weekly sweep.

## What is in this repository

| Path | Purpose |
|---|---|
| `go-inventory/` | A small Go API routed by gorilla/mux, with `go 1.21` in `go.mod` and a `golang:1.21-alpine` base image |
| `node-catalog/` | A small Express API that uses `dotenv`, `node-fetch`, `nodemon`, and `jest`, pinned to Node 18 |
| `.github/dependabot.yml` | Weekly Dependabot updates for Go modules, npm, Docker base images, and actions |
| `.github/workflows/capscan.md`, `capscan-sweep.md` | The capscan workflows, installed with `gh aw add stevehenderson/capscan@v1.0.0` |
| `.github/workflows/*.lock.yml` | The GitHub Actions workflows that `gh aw` compiled from them |
| `.github/aw/capscan/` | The rules and scripts the workflows use, installed by the same command |
| `.github/aw/packages/` | gh-aw's record of the installed package version and file digests |

Everything capscan-specific came from the one `gh aw add` command, and `gh aw update` moves it to newer capscan releases.
The [capscan repository](https://github.com/stevehenderson/capscan) explains how the workflows are built and how to run the same review on a workstation as an agent skill.

## The drift is intentional

Both services are written the way many real services were written a few years ago, so capscan has something to find:

- `go-inventory` declares `go 1.21`, which keeps the 1.21 router behavior and older TLS defaults even when a newer Go toolchain builds it.
  Go 1.22 can replace gorilla/mux with the standard library router, and the `item := item` copy in `reindex.go` is no longer needed.
- `node-catalog` targets Node 18.
  Current Node releases cover what `dotenv`, `node-fetch`, `nodemon`, and `jest` do here with `--env-file`, built-in `fetch`, `--watch`, and `node:test`.
  `npm audit` reports high-severity advisories that reach this project only through `nodemon` and `jest`, so adopting the built-ins would also remove them.

## Local development

```console
cd go-inventory && go test -race ./...
cd node-catalog && npm ci --ignore-scripts && npm test
```

## License

MIT
