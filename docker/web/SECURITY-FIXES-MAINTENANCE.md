# Release-based maintenance build for issues 397–400

Base: official v2.11.0. Independent upstream submissions: PRs #401, #402,
#403 and #404 (ingres-si/ingressi). Source changes are backported on this
tracked maintenance branch; only web is rebuilt. Keep official Caddy/L4 pins
and host-specific patched ClickHouse versions/configuration unchanged.

Build the committed checkout with docker/web/Dockerfile.securityfixes and
APP_VERSION=2.11.0-securityfixes.<short-commit>, recording the full commit,
resulting image ID and docker-save archive checksum. Bun's official base is
pinned by digest; application dependencies use the release lockfile.

Keep the canonical Compose web image pinned with pull_policy: never. For a
future release, verify each issue independently. Preserve any outstanding
source fixes by a tracked backport/rebuild and regression validation; stop on
conflicts/failures. Retire a patch only after the official release contains it
and its specific reproduction passes. Never overwrite a maintained build
with an official image missing one of the corrections, modify files inside
an image, or deploy a develop image. Keep rollback images and verified state.

The timestamp fix changes neither stored epochs nor existing partition
semantics. Legacy timestamp column timezones are intentionally retained;
fresh tables explicitly use UTC. Historical timestamp repair and repartitioning
need separate evidence and coordinated writers. Preserve existing UTC overrides.

Upgrade .147 first, then only .28's alternate-port Ingressi. Never cut over,
promote the prepared production Compose, or operate NPM/CrowdSec lifecycle.
