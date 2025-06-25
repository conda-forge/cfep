
<table>
<tr><td> Title </td><td> Make all Rust packages auditable </td>
<tr><td> Status </td><td> Proposed </td></tr>
<tr><td> Author(s) </td><td> Uwe L. Korn &lt;uwelk@xhochy.com&gt; </td></tr>
<tr><td> Created </td><td> Jun 23, 2025</td></tr>
<tr><td> Updated </td><td> Jun 23, 2025</td></tr>
<tr><td> Discussion </td><td> N/A </td></tr>
<tr><td> Implementation </td><td> <a href="https://github.com/conda-forge/rust-activation-feedstock/pull/79">rust-activation-feedstock#79</a> </td></tr>
</table>

## Abstract

This proposal suggests integrating [`cargo‑auditable`](https://github.com/rust-secure-code/cargo-auditable) into the build process for all Rust-based feedstocks on conda‑forge.
By enabling dependency‑tree embedding within compiled binaries, this integration would enhance downstream vulnerability auditing and transparency.

## Motivation

`cargo-auditable` embeds dependency information as a JSON blob into a dedicated linker section with [minimal overhead (\~4 kB), even for large dependency graphs (>400 crates)](https://github.com/rust-secure-code/cargo-auditable?tab=readme-ov-file#doesnt-this-bloat-my-binary).
This enables downstream tooling (e.g. `cargo audit bin`, Trivy, Syft) to perform highly accurate vulnerability scans.

This brings the Rust binaries on conda-forge en par with the Go binaries that have this enabled by default (see the output of `go version -m <binary>`).
Multiple distributions (Alpine, NixOS, openSUSE, Void, Chimera) already adopt `cargo-auditable` in Rust package builds (see [Adoption](https://github.com/rust-secure-code/cargo-auditable?tab=readme-ov-file#adoption))

## Proposal

### 1. Add `cargo-auditable` as a hard dependency in `rust-activation`

As we want to use this in all Rust builds, it should come as a dependency of the activation.
Furthermore, we should set there the environment variable `CARGO` to `cargo auditable`.

Implementation: [rust-activation-feedstock#79](https://github.com/conda-forge/rust-activation-feedstock/pull/79)

### 2. Builds should use `$CARGO` instead of `cargo`

Build scripts should be adapted to use `$CARGO` (or `%CARGO`) instead of calls to plain `cargo`.
While we could replace these calls also with calls to `cargo auditable`, this should serve as a more long-term solution if we want to supply more options.

The usage of the `CARGO` environment variable also ensures that Python build using `maturin` also pick up `cargo-auditable` automatically (see [maturin#495](https://github.com/PyO3/maturin/issues/2495)).

### 3. Add a lint to conda-smithy for `CARGO` usage

This should notify in most cases if people still continue to invoke `cargo` in recipes.

Implementation: [conda-smithy#2341](https://github.com/conda-forge/conda-smithy/pull/2341)

### 4. (optional) Add verification

If one wants to check whether the audit was written, one can run `rust-audit-info` on the binary.
In the successful case, it will report the data, otherwise it will exit with exit code 1.

```bash
rust-audit-info <binary>
```

This is an optional check for places where enabling dependency information has been more tricky.
We don't expect this check to be included as a default.

### 5. Run a migration to replace cargo calls

As a final step, we should run a migration that replaces all occurences of `cargo` in recipes with the variable.
By this, we can easily make all latest builds auditable and remove the burden from the individual maintainers to touch the recipe.

Implemenation: _TODO_

## Future Extensions

In future, we may also want to ship SBOMs as part of conda packages.
We can generate them using a generic tool like [`syft`](https://github.com/anchore/syft) from the binary data or a more specific one like [`cargo-cyclonedx`](https://github.com/CycloneDX/cyclonedx-rust-cargo).

While `cargo-cyclonedx` is able to generate the SBOM from the `Cargo.toml` instead of the binary, keeping the dependency information in the binary allows one to check the SBOM for correctness.
The benefit of an SBOM over the binary dependency data is that it can be scanned faster.

An example execution of `cargo-cyclonedx` might look as follows:

```bash
cargo cyclonedx --target $CARGO_BUILD_TARGET --no-build-deps --describe binaries --format json
```

The usage of SBOMs in conda packages is dependent on a separate, soon-to-be-discussed conda enhancement proposal.
