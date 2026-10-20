# Tandevia Workspace

The shared environment where people and AI agents work together: chat with any
model, build and share agents, search every conversation, connect MCP tools.
All model traffic goes through [Tandevia Gateway](https://github.com/Tandevia/bifrost).

## Run it

The image is built by GitHub Actions on every push to `tandevia` and published as
`ghcr.io/tandevia/librechat:tandevia`. The production deployment on ai.forscope.eu
is described in [deploy/ai.forscope.eu](deploy/ai.forscope.eu/README.md).

## Branches

- `tandevia` (default): Tandevia Workspace, rebranded.
- `main`: mirrors upstream LibreChat. Releases are tags `vX.Y.Z`; merge the next tag
  into `tandevia` to update (`tandevia` was started from `v0.8.8`).

The rebrand touches the product name, logos, favicons and a handful of UI strings.
Configuration keys, the API and the data model are unchanged, so upstream
documentation and upstream merges keep working.

## License and origin

Tandevia Workspace is a modified version of [LibreChat](https://github.com/LibreChat-AI/LibreChat),
Copyright (c) 2026 LibreChat, used under the [MIT License](LICENSE). The original README is
kept as [README.upstream.md](README.upstream.md).
