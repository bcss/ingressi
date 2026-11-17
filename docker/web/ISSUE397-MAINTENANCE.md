# Temporary maintenance build for upstream issue 397

This branch is based on the official v2.11.0 tag, with the transaction-ID fix
backported from upstream pull request https://github.com/ingres-si/ingressi/pull/401.
Only the web image is rebuilt. Caddy and L4 remain on matching official 2.11.0
images; the existing Coraza-Caddy module already exports the required ID.

Build from this checked-out branch, never modify a running image:

    docker build -f docker/web/Dockerfile.issue397 \
      --build-arg APP_VERSION=2.11.0-issue397.6e14ccc0 \
      -t local/ingressi-web:2.11.0-issue397-6e14ccc0 .

The Bun base is pinned by digest. Record the resulting image ID and archive
checksum, retain an off-host docker-save archive and verified pre-upgrade state.
Compose must pin the maintenance image with `pull_policy: never` while this fix
has not shipped in a tested official release. Do not promote the prepared
production-port compose file.

For a later release, verify whether #397 is actually included. If not, backport
the source fix onto that release and rerun its regression suite before deploying.
Stop on conflicts or failed tests. If included, use verified official images and
rerun the exact-ID live reproduction before retiring this maintenance build.
A floating upstream image is not a safe upgrade path while the fix is absent.

No schema migration is introduced by this fix. Existing stored outcomes are
not repaired. Legacy access rows without the ID are conservatively unattributed.
