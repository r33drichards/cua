# CUA CLI (Bun)

## Install

```bash
bun install
bun link           # register package globally
bun link cua-cli   # install the global binary `cua`
```

If you want to run without linking:

```bash
bun run ./index.ts -- --help
```

## Commands

- **Auth**
  - `cua auth login` – opens browser to authorize; stores API key locally
  - `cua auth login --api-key sk-...` – stores provided key directly
  - `cua auth pull` – writes/updates `.env` with `CUA_API_KEY`
  - `cua auth logout` – clears stored API key

- **VMs**
  - `cua vm list`
  - `cua vm start NAME`
  - `cua vm stop NAME`
  - `cua vm restart NAME`

## Auth Flow (Dynamic Callback Port)

- CLI starts a small local HTTP server using `Bun.serve({ port: 0 })` which picks an available port.
- Browser is opened to `https://cua.ai/cli-auth?callback_url=http://127.0.0.1:<port>/callback`.
- After you click "Authorize CLI", the browser redirects to the local server with `?token=...`.
- The CLI saves the API key in `~/.config/cua/cli.sqlite`.

> Note: If the browser cannot be opened automatically, copy/paste the printed URL.

## Project Structure

- `index.ts` – entry point (shebang + start CLI)
- `src/cli.ts` – yargs bootstrapping
- `src/commands/auth.ts` – auth/login/pull/logout commands
- `src/commands/vm.ts` – vm list/start/stop/restart commands
- `src/auth.ts` – browser flow + local callback server (dynamic port)
- `src/http.ts` – HTTP helper
- `src/storage.ts` – SQLite-backed key-value storage
- `src/config.ts` – constants and paths
- `src/util.ts` – table printing, .env writer

## Notes

- Stored API key lives at `~/.config/cua/cli.sqlite` under `kv(api_key)`.
- Public API base: `https://api.cua.ai`.
- Authorization header: `Authorization: Bearer <api_key>`.
