# SovereignBunker

SovereignBunker is Sovereign Technology's maintained fork of
[Bunker46](https://github.com/dsbaars/bunker46), the NIP-46 remote signer by Djuri Baars (MIT). We run it
as our production signer. It stays mergeable with upstream: we merge upstream releases in, offer our fixes
back as pull requests, and keep the diff small.

## Where it lives

| What                                   | Where                                                  |
| -------------------------------------- | ------------------------------------------------------ |
| Canonical repo (CI, merges, releases)  | GitLab `sovtech/sovereignbunker`                       |
| Public copy and upstream pull requests | https://github.com/SovereignTechnology/sovereignbunker |
| Upstream                               | https://github.com/dsbaars/bunker46                    |

The public GitHub copy receives `main` and `sovtech-v*` release tags only. GitHub Actions is disabled there
on purpose: CI and release builds run only in our own infrastructure (see below).

## What differs from upstream

- **Carried fixes** that we also offered upstream, listed newest first:
  - `connect` replies `ack` unless the request presents the connection's own secret. This is upstream
    PR #26; rust-nostr clients such as ngit 3.x need it to sign at all.
  - A relay connect timeout ("WebSocket was closed before the connection was established") is treated as
    transient, not fatal. This is upstream PR #25, which fixes #24.
- **Branding:** the web UI title, header, login and register screens and the API docs title say
  SovereignBunker.
- **Builds:** the Dockerfile base images are pinned by digest.

### What deliberately still says `bunker46`

These are identifiers, not branding. Renaming them would break running installs or make every upstream
merge conflict:

- the `@bunker46/*` package names;
- the database and role names;
- environment variable keys;
- browser storage keys (`bunker46-settings`, `bunker46-ui`);
- the event channel prefix;
- the TOTP issuer and WebAuthn RP name (changing them would split existing authenticator entries);
- the key-derivation salt `bunker46:encryption-key:v2`. Changing it makes every stored key
  undecryptable, so never touch it.

## Merging

- Our own changes go through merge requests and are squashed into one commit authored
  `sovtech <git@sovtech.pro>`.
- Upstream releases come in as a real two-parent merge of `upstream/main`, never a squash, so later merges
  stay clean.
- A change we want upstream is also opened as a pull request against `dsbaars/bunker46` from a branch based
  on upstream `main`.

## CI and releases

- CI runs only as GitLab jobs in fresh Kata VMs on our home runners, never on rented hosts. The release
  image is the one that holds our signing keys, so no third party gets to touch its build.
- Release tags are `sovtech-v*`. A release pipeline builds the server and web OCI images from source and
  publishes them with a `SHA256SUMS` file. Our deploy verifies each image against it before import.

## License

MIT, as upstream. See [LICENSE](LICENSE); Djuri Baars' copyright notice is kept, and ours covers the
SovereignBunker changes.
