# WebsiteCore

WebsiteCore is a community platform (a fork of paopao-ce): feeds, topics, comments, users, content moderation, and search. This file is the shared vocabulary for the project — what terms mean *here*, not general programming concepts.

## Language

### Dependencies

**Backing service**:
An external process the API talks to over the network at runtime — the database, Redis, or the search engine.
_Avoid_: dependency, middleware, infrastructure

**Dev dependency stack**:
The backing services started together by `docker-compose.dev.yml` for local development: Postgres, Redis, and Meilisearch. The application itself is not part of it.
_Avoid_: local environment, dev containers, infra stack, sidecar

**Object storage**:
Where uploaded media is persisted. `LocalOSS` (the filesystem) is the development default; MinIO, S3, and the cloud OSS/COS/OBS providers are optional alternatives.
_Avoid_: blob store, file service

### Configuration

**Feature suite**:
The `Features.Default` list in `config.yaml` that selects which runtime capabilities are active (for example `Web`, `Meili`, `Migration`).
_Avoid_: plugins, modules, flags

**Bootstrap config**:
The tracked `config.yaml.sample` — the minimal, file-based settings a fresh checkout needs. An untracked `config.yaml` overrides it locally.
_Avoid_: default config, base config

### Data and schema

**Migration feature**:
The `Migration` entry in the feature suite. It decides *whether* migrations run at startup.
_Avoid_: auto-migrate

**Migration build tag**:
The Go build tag `migration`, which embeds the migration files into the binary. It decides whether migration support is *compiled in*. It is independent of the Migration feature; startup migration requires both.
_Avoid_: migration flag

### Identity and access

**Platform management role**:
An account classification for operational authority across the platform. It is distinct from a member identity.
_Avoid_: Identity, teacher role, mentor role

**Member identity**:
The exactly-one classification of an ordinary registered member as a Student or Teacher.
_Avoid_: Management role, permission group

**Student**:
The member identity for a young participant who creates projects and joins courses.
_Avoid_: Fellow, regular user

**Teacher**:
The member identity for an adult qualified to teach or guide; a Teacher need not currently teach a Course.
_Avoid_: Course instructor, mentor

**Mentor designation**:
An additional designation held only by a Teacher. It is neither a platform management role nor a member identity.
_Avoid_: Mentor role, mentor identity

**Course instructor assignment**:
The relationship between a Teacher and a Course identifying who teaches it. The relationship does not define whether the member is a Teacher.
_Avoid_: Teacher identity, teacher role

**Visitor**:
A person browsing without an authenticated member account. Visitor is not a member identity.
_Avoid_: Guest account, unverified member
