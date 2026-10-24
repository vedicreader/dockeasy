# Release notes

<!-- do not remove -->

## 0.0.12
- `secret_set` defaults to `save=False`. When the keychain fails it raises `RuntimeError` and writes nothing. It saved the value in plaintext in the `.env` file without a message.
- `secret_set` writes the `.env` file and `os.environ` only with `save=True`. It also wrote them by default when the keychain worked.
- `secret_set` with `save=True` issues a `UserWarning` that names the `.env` file when the keychain fails, then saves the value there in plaintext.
- `mk_flags` writes the integer values `0` and `1` as values, as in `--quiet=0` and `--retries=1`. It treated them as `False` and `True`.
- `build` passes the Dockerfile name `fn` to Compose as `dockerfile`.
- `drun` keeps the protocol suffix of a container port, as in `{'53/udp': 5353}`.
- `Compose.load` keeps top-level keys other than `services`, `networks` and `volumes`, and `to_dict` writes them back.
- `env_set` creates a new `.env` file with mode `0600` in one step.
- `caddy_svc` mounts a relative `conf` path under its folders, as in `./conf/Caddyfile`. It mounted the file name only.
- The compose command style is probed once per process. `_compose_cmd.cache_clear()` probes again.

## 0.0.11
- `Caddyfile.crowdsec` writes the global `order crowdsec first`, which Caddy needs to place the plugin's handler. `api_url` and `api_key` are written in the global `crowdsec` block, where the plugin reads them. The site block contains a bare `crowdsec` handler.
- `python_app`, `fasthtml_app` and `fastapi_react` health checks use a Python `urllib` one-liner. The slim image does not include curl. Installing curl through `pkgs` selects the curl health check.

## 0.0.10
- `FASTOPS_ENV` names the default `.env` file `env_set` and `env_get` use; `_FASTOPS_ENV` remains as an alias

## 0.0.9
- `Caddyfile.cloudflared` also trusts private-range proxies and reads the client address from `Cf-Connecting-Ip`, in a global `servers` block
- `Caddyfile.encode` takes the encodings in order, as in `encode('zstd', 'gzip')`
- `Caddyfile.upstreams` forwards to the app on several ports with `reverse_proxy` load-balancing and health-check options, inside a fallback `handle` block with `handle=True`
- `Caddyfile.raw`, `Caddyfile.comment` and `Caddyfile.notes` add raw blocks, comment lines and end-of-line comments
- A rendered Caddyfile ends with a newline and has a blank line after the global block
- `caddy_svc` takes `caddyfile` to write your own Caddyfile, `save=False` to write none, and `mount` for the mounted path
- `inst_uv` takes `lock`, `bytecode`, `deps_first`, `mid` and `compile`; `uv_deps` and `uv_project` are its two halves
- `python_app(multistage=False, lock=True)` is a locked single-stage recipe pulling only from Docker Hub; `ports` exposes consecutive worker ports and `pre`, `mid` and `compile` add steps

## 0.0.8
- `Caddyfile.log` takes `roll_size`, `roll_keep`, `roll_keep_for` and `format`

## 0.0.7
- `pyyaml` is a declared dependency
- `Caddyfile.log(output=path)` writes `output file <path>`, which Caddy accepts

## 0.0.6
secrets fixed

## 0.0.5
pip install uv



## 0.0.4
skills




## 0.0.3
release



## 0.0.2
caddy, crowdsec and cloudflared



## 0.0.1
initial release
