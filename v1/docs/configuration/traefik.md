---
title: Traefik
---

# Traefik

Traefik is a reverse proxy, used by Kamal for zero-downtime deployments.

We start an instance on the hosts in it's own container.

During a deployment:
1. We start a new container which Traefik automatically detects due to the labels we have applied
2. Traefik starts routing traffic to the new container
3. We force the old container to fail it's healthcheck, causing Traefik to stop routing traffic to it
4. We stop the old container

## [Traefik settings](#traefik-settings)

Traekik is configured in the root configuration under `traefik`.

```yaml
traefik:
```

## [Image](#image)

The Traefik image to use, defaults to `traefik:v2.10`:

```yaml
  image: traefik:v2.9
```

## [Host port](#host-port)

The host port to publish the Traefik container on, defaults to `80`:

```yaml
  host_port: "8080"
```

## [Disabling publishing](#disabling-publishing)

To avoid publishing the Traefik container, set this to `false`:

```yaml
  publish: false
```

## [Labels](#labels)

Additional labels to apply to the Traefik container:

```yaml
  labels:
    traefik.http.routers.catchall.entryPoints: http
    traefik.http.routers.catchall.rule: PathPrefix(`/`)
    traefik.http.routers.catchall.service: unavailable
    traefik.http.routers.catchall.priority: "1"
    traefik.http.services.unavailable.loadbalancer.server.port: "0"
```

## [Arguments](#arguments)

Additional arguments to pass to the Traefik container:

```yaml
  args:
    entryPoints.http.address: ":80"
    entryPoints.http.forwardedHeaders.insecure: true
    accesslog: true
    accesslog.format: json
```

## [Options](#options)

Additional options to pass to `docker run`:

```yaml
  options:
    cpus: 2
```

## [Environment variables](#environment-variables)

See [Environment variables](../environment-variables):

```yaml
  env:
    ...
```
