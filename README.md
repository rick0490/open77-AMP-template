# OPEN//77 template for CubeCoders AMP

An [AMP](https://cubecoders.com/AMP) Generic module template for the
[OPEN//77](https://open2077.net/host) dedicated server (Cyberpunk 2077 multiplayer).

AMP downloads the official server build from the OPEN//77 CDN, keeps it updated,
and starts it in hosting-panel mode (`--no-setup`), so the browser setup wizard is never needed.

## Files

| File | Purpose |
| --- | --- |
| `open77.kvp` | Main definition: executable, startup arguments, stop method, console regexes |
| `open77config.json` | Settings shown in the AMP panel |
| `open77ports.json` | Ports |
| `open77updates.json` | Install/update stages (reads `https://cdn.open77.dev/server/latest.json`) |
| `open77metaconfig.json` | Empty: every setting is passed on the command line, so no config file is edited |
| `manifest.json` | Repository manifest. ADS needs it (`"repotype": "AppTemplates"`) to load templates from this repo |

## Adding it to AMP

1. In the ADS instance, open **Configuration → Instance Deployment → Configuration Repositories**.
2. Keep `CubeCoders/AMPTemplates:main` and add `<your-github-user>/<this-repo>:main`.
3. Save and fetch the latest templates. **OPEN//77** now appears when you create an instance.

The repository must be public, with the template files in its root.

## Setting up an instance

1. Create a license key at <https://open2077.net/account/keys>. It is only shown once.
2. In the instance settings, fill in **License Key** and **Public IP**. The server does not detect its own public IP.
3. Run **Update**, then **Start**.

The license key and database connection string are passed as the `OP77_LICENSE_KEY` and
`OP77_DATABASE_CONNECTION` environment variables, not as command-line arguments.

## Ports

| Port | Protocol | Use |
| --- | --- | --- |
| 11778 | UDP | Game traffic |
| 11779 | TCP | Resource downloads and public status query. Must be reachable by players |
| 11780 | TCP | Warden web admin panel (only when enabled). Keep it private or behind a TLS reverse proxy |

## Notes

- **Console is read-only.** The server ignores commands sent to its standard input. Use the Warden
  panel (setting: *Enable Warden Admin Panel*) for admin commands. On first start with Warden enabled,
  a one-time setup PIN is printed to the AMP console.
- **Stopping** uses `OS_CLOSE` (Ctrl+C / SIGINT). The server ignores SIGTERM.
- **Updates** extract the official archive over the install. The archive contains no runtime state,
  so `server.jsonc`, `acl.jsonc`, `.open77/` and resource `data/` folders are kept. Local edits to
  bundled resources are overwritten.
- **Settings precedence:** AMP settings are passed as startup overrides, which win over `server.jsonc`.
  Changes made in Warden to the same fields are overridden at the next start.
- **Not yet done:** `Console.UserJoinRegex` / `Console.UserLeaveRegex` are empty because the join and
  leave log lines have not been captured from a real client yet, so AMP does not track online players.
- **Tested so far:** on Linux, the server binary validates the command line AMP builds
  (`--check-config`), and the ready regex matches the server's real log output. The template has not
  yet been run end to end inside AMP, and Windows is untested.

## License

The OPEN//77 server is licensed under the
[PolyForm Noncommercial License 1.0.0](https://polyformproject.org/licenses/noncommercial/1.0.0),
so this template sets `Meta.NoCommercialUsage=True`. This template only downloads the official
build. It does not redistribute it.
