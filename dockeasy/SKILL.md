# dockeasy

dockeasy writes Dockerfiles, Caddyfiles and Docker Compose files from Python. It also runs Docker containers and stores secrets.

## Framework builders

```python
from dockeasy import fasthtml_app, python_app, go_app, detect_app

df = fasthtml_app()                                      # FastHTML, port 5001, uv, single-stage
df = fasthtml_app(pkgs=['rclone'], vols=['/app/data'],
                  healthcheck='/health', cmd=['python', 'main.py'])
df = python_app()                                        # port 8000, uv, multi-stage
df = python_app(pkgs=['httpx'], vols=['/app/data'])
df = go_app()                                            # builder + distroless runtime
df = detect_app('.')                                     # sniffs go.mod, Cargo.toml, package.json, pyproject.toml
```

## Dockerfile builder (low-level)

```python
from dockeasy import Dockerfile

df = (Dockerfile()
      .from_('python:3.13-slim')
      .workdir('/app')
      .copy('pyproject.toml', '.')
      .run_mount('uv sync --frozen', target='/root/.cache/uv')   # --mount=type=cache
      .copy('.', '.')
      .expose(8000)
      .cmd(['uv', 'run', 'python', '-m', 'myapp']))
```

`run_mount(cmd, target)` adds `--mount=type=cache` automatically. Docker keeps the cache between builds.

## Building and running containers

```python
from dockeasy import drun, containers, logs, stop, rm

tag = df.build(tag='myapp:latest', path='.')
cid = drun(tag, detach=True, ports={5001: 5001}, name='myapp', check=True)
print(logs('myapp', n=20))
stop('myapp')
rm('myapp')
```

## Caddy

```python
from dockeasy import Compose, caddy_svc, cloudflared_svc, crowdsec

# Direct: Caddy auto-TLS, ports 80 and 443 open
dc = (Compose()
      .svc('app', build='.', networks=['web'], restart='unless-stopped')
      .svc('caddy', **caddy_svc('myapp.com', port=5001))
      .network('web').volume('caddy_data').volume('caddy_config'))

# Cloudflare tunnel: no open ports
dc = (Compose()
      .svc('app', build='.', networks=['web'], restart='unless-stopped')
      .svc('caddy', **caddy_svc('myapp.com', cloudflared=True))
      .svc('cloudflared', **cloudflared_svc())
      .network('web').volume('caddy_data').volume('caddy_config'))

# CrowdSec + tunnel: blocks known attackers, no open ports
dc = (Compose()
      .svc('app', build='.', networks=['web'], restart='unless-stopped')
      .svc('caddy', **caddy_svc('myapp.com', crowdsec=True, cloudflared=True))
      .svc('crowdsec', **crowdsec())
      .svc('cloudflared', **cloudflared_svc())
      .network('web')
      .volume('caddy_data').volume('caddy_config')
      .volume('crowdsec-db').volume('crowdsec-config'))
```

CrowdSec records decisions to block client IP addresses. Caddy checks these decisions before it forwards a request. The Cloudflare Tunnel needs no inbound ports.

`caddy_svc()` also accepts:
- `dns='cloudflare'`: uses Cloudflare DNS to prove domain control. DNS-01 supports wildcard certificates. It does not require an inbound connection on port 80.
- `caddy_api()`: preset with rate limiting and body size cap

## caddy_stack shortcut

```python
from dockeasy import fasthtml_app
from vpseasy import caddy_stack
from fastcore.all import joins

compose = caddy_stack(joins('.', ['lego', 'sankalpa.sh']), fasthtml_app(), vols=['/app/data'])
```

`caddy_stack` lives in vpseasy. It combines `caddy_svc` and `cloudflared_svc` into a Compose file ready to deploy.

## Compose (low-level)

```python
from dockeasy import Compose

c = (Compose()
     .svc('web', build='.', ports={8000: 8000})
     .svc('db', image='postgres:16', env={'POSTGRES_DB': 'app'})
     .volume('pgdata'))
```

## Secrets

```python
from dockeasy import env_set, env_get, secret_set, secret_get

env_set('VPS_IP', '1.2.3.4')          # ~/.config/fastops/.env, mode 0600
env_set('KEY', 'val', path='.env')    # write to specific file instead
secret_set('JWT_SCRT', 'abc123')      # OS keychain only; RuntimeError when there is none
secret_set('JWT_SCRT', 'abc123', save=True)  # also plaintext .env and os.environ; UserWarning when the keychain failed

print(env_get('VPS_IP'))
print(secret_get('JWT_SCRT'))
```

Dependencies: `fastcore`, `keyring`, `python-dotenv` and `pyyaml`.
