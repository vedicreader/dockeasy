# Release notes

<!-- do not remove -->

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
