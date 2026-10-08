# HEX Agent Guide
github: octocat
name: Mona Lisa Octocat
bio: I build web apps and I'm learning Go.
location: Yaoundé, Cameroon
website: https://octocat.dev
contributions: none
## Project Scope

HEX is a local-first voice dictation application. The native application and
runtime are implemented in Rust; macOS is the primary platform, with an
explicitly limited x86_64 Linux beta for X11 and compatible wlroots Wayland
compositors. Keep platform support and feature availability aligned with the
published guides. Do not imply support for untested desktops or platforms.

Start with [`CONTRIBUTING.md`](CONTRIBUTING.md) for setup and validation.
[`docs/features/README.md`](docs/features/README.md) maps shipped capabilities
to their source, checks, and known limits. Read the relevant feature guide
before changing user-visible behavior. Treat source and tests as the evidence
for current behavior; plans, prototypes, and release notes may be historical or
describe future work.

## Design And Behavior

- Keep the Rust runtime authoritative for native behavior and safety-critical
  command handling. The TypeScript command SDK is for user-configured commands
  and transformations; do not move protected native behavior into user config.
- Commands and Voice Action are separate opt-ins and default to off. Preserve
  explicit consent for features that execute user configuration or send data
  to an external provider.
- Keep microphone capture responsive: inference, post-processing, paste, and
  UI work must not block the capture path. Bound queues and preserve established
  cancellation, ordering, and fallback behavior.
- Preserve platform boundaries. Linux X11/Wayland behavior is not a promise of
  general Wayland support; consult [`docs/linux.md`](docs/linux.md) and
  [`docs/nix.md`](docs/nix.md) before changing Linux runtime or packaging.
- Prefer focused changes using existing patterns. Do not introduce a platform,
  provider, or plugin abstraction without a second real implementation that
  needs it.

## Documentation

When a change affects a user-visible capability, update the corresponding
feature map or guide in the same change. Keep defaults, prerequisites,
platforms, recovery behavior, and available verification accurate. Do not
describe a test as passing unless it was run, or treat a UI preview as
end-to-end platform validation. Put future work in [`ROADMAP.md`](ROADMAP.md).

## Validation

Run the narrowest relevant checks, and run the standard Rust checks for native
changes:

```sh
cargo fmt --check
cargo test
cargo clippy --all-targets --all-features -- -D warnings
git diff --check
```

For public TypeScript SDK changes, follow
[`sdk/typescript/README.md`](sdk/typescript/README.md); at minimum run its
`check`, `test`, and `build` scripts. For personal command SDK changes, build
the workspace package with `bun run --cwd sdk/commands build`. Platform-specific
validation and required system dependencies are documented in
[`CONTRIBUTING.md`](CONTRIBUTING.md), [`docs/linux.md`](docs/linux.md), and
[`docs/nix.md`](docs/nix.md).
