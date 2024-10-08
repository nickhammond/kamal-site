---
title: Builder
---

# Builder

The builder configuration controls how the application is built with `docker build` or `docker buildx build`. If no configuration is specified, Kamal will:

1. Create a buildx context called `kamal-<service>-multiarch`
2. Use `docker buildx build` to build a multiarch image for linux/amd64,linux/arm64 with that context

See [Builder examples](/docs/configuration/builder-examples/) for more information.

## [Builder options](#builder-options)

Options go under the builder key in the root configuration:

```yaml
builder:
```

## [Multiarch](#multiarch)

Enables multiarch builds, defaults to `true`:

```yaml
  multiarch: false
```

## [Local configuration](#local-configuration)

The build configuration for local builds, only used if multiarch is enabled (the default).

If there is no remote configuration, by default we build for amd64 and arm64. If you only want to build for one architecture, you can specify it here. The Docker socket is optional and uses the default Docker host socket when not specified:

```yaml
  local:
    arch: amd64
    host: /var/run/docker.sock
```

## [Remote configuration](#remote-configuration)

The build configuration for remote builds, also only used if multiarch is enabled. The arch is required and can be either amd64 or arm64:

```yaml
  remote:
    arch: arm64
    host: ssh://docker@docker-builder
```

## [Builder cache](#builder-cache)

The type must be either 'gha' or 'registry'. The image is only used for registry cache:

```yaml
  cache:
    type: registry
    options: mode=max
    image: kamal-app-build-cache
```

## [Build context](#build-context)

If this is not set, then a local Git clone of the repo is used. This ensures a clean build with no uncommitted changes.

To use the local checkout instead you can set the context to `.`, or a path to another directory:

```yaml
  context: .
```

## [Dockerfile](#dockerfile)

The Dockerfile to use for building, defaults to `Dockerfile`:

```yaml
  dockerfile: Dockerfile.production
```

## [Build target](#build-target)

If not set, then the default target is used:

```yaml
  target: production
```

## [Build arguments](#build-arguments)

Any additional build arguments, passed to `docker build` with `--build-arg <key>=<value>`:

```yaml
  args:
    ENVIRONMENT: production
```

## [Referencing build arguments](#referencing-build-arguments)

```shell
ARG RUBY_VERSION
FROM ruby:$RUBY_VERSION-slim as base
```

## [Build secrets](#build-secrets)

Values are read from the environment:

```yaml
  secrets:
    - SECRET1
    - SECRET2
```

## [Referencing build secrets](#referencing-build-secrets)

```shell
# Copy Gemfiles
COPY Gemfile Gemfile.lock ./

# Install dependencies, including private repositories via access token
# Then remove bundle cache with exposed GITHUB_TOKEN)
RUN --mount=type=secret,id=GITHUB_TOKEN \
  BUNDLE_GITHUB__COM=x-access-token:$(cat /run/secrets/GITHUB_TOKEN) \
  bundle install && \
  rm -rf /usr/local/bundle/cache
```

## [SSH](#ssh)

SSH agent socket or keys to expose to the build:

```yaml
  ssh: default=$SSH_AUTH_SOCK
```
