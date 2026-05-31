# AFFiNE MCP Server SDK for Workshop

This SDK provides the [AFFiNE](https://affine.pro/) MCP server, which exposes
AFFiNE workspaces and documents to AI assistants over stdio (default) or HTTP.
It ships the `affine-mcp` CLI with a bundled Node.js runtime and persists
credentials and configuration on the host across workshop updates. The server
connects to AFFiNE Cloud or a self-hosted instance via its GraphQL and
WebSocket APIs.

---

## Reference workshop

A minimal workshop:

```yaml
# workshop.yaml
name: affine-mcp-app
base: ubuntu@24.04
sdks:
  - name: affine-mcp-server
    channel: latest/stable

actions:
  login: |
    affine-mcp login
  status: |
    affine-mcp status
```

This demonstrates authenticating against an AFFiNE instance and verifying the
configuration, with credentials persisted across workshop updates.

---

## Using the SDK

### Prerequisites, project layout

1. No prerequisite SDKs are required.
2. No specific project layout is needed; the SDK provides the `affine-mcp` CLI.
3. On launch, the SDK puts `affine-mcp` on `PATH` and ensures the configuration
   directory `~/.config/affine-mcp` exists with restrictive permissions.

### Authenticate

Connect the server to your AFFiNE instance. Credentials are stored in
`~/.config/affine-mcp/config` (mode 600), which is mapped to the host via the
`affine-mcp-config` mount plug and reused across workshop updates.

```bash
workshop shell
affine-mcp login
affine-mcp status
```

You can also configure the server with environment variables instead of
`login`, for example `AFFINE_BASE_URL`, `AFFINE_API_TOKEN`, and
`AFFINE_TOOL_PROFILE` (`read_only`, `core`, `authoring`, or `full`).

### Connect an AI client (stdio)

The default transport is stdio: an AI client (such as Claude Code) spawns
`affine-mcp` as a subprocess. Generate a ready-made client configuration
snippet:

```bash
workshop shell
affine-mcp snippet claude
```

### Run as an HTTP server (optional)

The server can instead listen for MCP requests over HTTP on port 3000
(`/mcp`, with `/healthz` and `/readyz` health endpoints). Set an auth token and
start it:

```bash
workshop shell
MCP_TRANSPORT=http \
  AFFINE_MCP_AUTH_MODE=bearer \
  AFFINE_MCP_HTTP_TOKEN=your-strong-secret \
  affine-mcp
```

Host tools and other SDKs can reach this server through the `affine-mcp-http`
tunnel slot on port 3000.

### Verify from the command line

```bash
workshop shell
affine-mcp --version
affine-mcp doctor
```

`affine-mcp doctor` diagnoses connectivity and configuration issues.

---

## Plugs (resources this SDK consumes)

### `affine-mcp-config`

- Interface: `mount`
- Workshop target: `/home/workshop/.config/affine-mcp`
- Mode: `0o700`
- Purpose: Persists the `affine-mcp` credentials and configuration between
  workshop updates so you do not need to re-authenticate after every refresh.

## Slots (resources this SDK provides)

### `affine-mcp-http`

- Interface: `tunnel`
- Endpoint: `3000`
- Purpose: Exposes the MCP server's HTTP endpoint (when run with
  `MCP_TRANSPORT=http`) for use by other SDKs or host tools.

---

## Documentation and guidance

- [AFFiNE MCP server repository](https://github.com/DAWNCR0W/affine-mcp-server)
- [AFFiNE documentation](https://docs.affine.pro/)
- [Model Context Protocol](https://modelcontextprotocol.io/)
- [Workshop documentation](https://ubuntu.com/workshop/docs/)

---

## Community and support

- AFFiNE community:
  [AFFiNE on GitHub](https://github.com/toeverything/AFFiNE)
- Workshop forum:
  [Discourse](https://discourse.ubuntu.com/)
- Please review our
  [Code of Conduct](https://ubuntu.com/community/ethos/code-of-conduct) before
  participating.

---

## Contributions

All contributions, including code, documentation updates, and issue reports,
are welcome!

- See `CONTRIBUTING.md` for guidelines.
- Open issues or pull requests on the official repository.

---

## License and copyright

Copyright 2026 Canonical Ltd.

This SDK is licensed under the MIT License.

The AFFiNE MCP server is licensed under the
[MIT License](https://github.com/DAWNCR0W/affine-mcp-server/blob/main/LICENSE).
