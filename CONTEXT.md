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
An Operator, Administrator, or Auditor classification granting operational authority across the platform. It is distinct from a member identity.
_Avoid_: Identity, teacher role, mentor role

**Dedicated management account**:
An account used exclusively for the Operator or Administrator role and carrying no Member identity. It may maintain a public profile and perform general community actions, but cannot perform Student- or Teacher-specific actions or be converted into a Member; removing its final management role deactivates the account.
_Avoid_: Staff member, administrator member

**Suspended account**:
An account temporarily prevented from signing in or acting while its identity, designations, historical records, and owned relationships remain intact. Suspending a Teacher does not hide the Teacher's Courses; management can still transfer them.
_Avoid_: Deleted account, role removal

**Soft-deleted account**:
An account hidden from normal use and discovery while retained for possible recovery. A Teacher cannot be soft-deleted while still assigned to a Course or carrying the Mentor designation.
_Avoid_: Suspended account, hard deletion

**Operator**:
A platform management role for system-wide operational authority. It includes Administrator and content-moderation capabilities without storing redundant Administrator or Auditor roles. An Operator uses a dedicated management account, not a member account.
_Avoid_: Manager, owner

**Administrator**:
A platform management role for user and platform administration. It includes content-moderation capability without storing a redundant Auditor role. An Administrator uses a dedicated management account, not a member account.
_Avoid_: Admin identity, teacher administrator

**Auditor**:
A platform management role that grants content-moderation authority to a Member. It is not displayed publicly. Operators and Administrators have the same authority without holding the Auditor role.
_Avoid_: Reviewer identity, moderator identity

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

**Conversation request**:
The first private message an eligible Member sends to another account when they have no established conversation. It remains pending and prevents further messages from that sender until the recipient replies.
_Avoid_: Chat request, friend request

**Established conversation**:
A private-message relationship in which both participants have sent at least one message. Eligible participants may continue messaging until either one blocks the other.
_Avoid_: Friendship, follow relationship

**Private-message block**:
A Member-controlled restriction that prevents private messages in both directions while preserving conversation history. It does not block messages from Operators or Administrators.
_Avoid_: Delete conversation, report user

### Content moderation

**Pre-publication review**:
A moderation policy that withholds member content from public view until it is approved.
_Avoid_: Post review, delayed publishing

**Post-publication review**:
A moderation policy that publishes content or profile changes immediately while keeping them subject to later review and enforcement. Teacher-authored content and profile changes use this policy, including those from Teachers with the Mentor designation.
_Avoid_: Exempt from moderation, no review
