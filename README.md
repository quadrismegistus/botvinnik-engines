# botvinnik-engines

CI-compiled, ad-hoc-signed **macOS** builds of open-source UCI chess engines that
ship no macOS binary of their own, hosted for one-tap download in
[botvinnik](https://botvinnik.app).

We do not modify these engines. Each build is compiled unchanged from the
upstream source at a pinned commit by [the build workflow](.github/workflows/build-engines.yml),
ad-hoc signed so Apple Silicon will launch it, and published here as a release
with its SHA-256. botvinnik's engine catalog pins that checksum and refuses any
download that does not match.

## Engines built here

| engine | upstream | licence |
| --- | --- | --- |
| Patricia | https://github.com/Adam-Kulju/Patricia | MIT |
| Clockwork | https://github.com/official-clockwork/Clockwork | AGPL-3.0 |

The upstream repositories are the source of truth. For the AGPL-3.0 engine the
corresponding source is the linked repository (§13); we redistribute only the
compiled binary and always link back to it.

## Model weights hosted here

Not everything here is a compiled binary. One release is a neural network:

| model | upstream | licence |
| --- | --- | --- |
| Chess-GPT (8-layer, Lichess) | https://huggingface.co/adamkarvonen/chess_llms | MIT |

`chessgpt-lichess-8layers-int8.onnx` is Adam Karvonen's Chess-GPT checkpoint
exported to ONNX and int8-quantised (~26 MB, down from ~103 MB) so it runs on a
phone through onnxruntime. It is a format change, not a retrain: the model is
the same one described in [arXiv:2403.15498](https://arxiv.org/abs/2403.15498),
a character-level transformer over PGN movetext that plays chess with no search
of any kind. Attribution and the licence reconstruction are in
[LICENSE-chessgpt](LICENSE-chessgpt).

Same rules as the binaries: the upstream repository is the source of truth, and
botvinnik's catalog pins the SHA-256 and refuses any download that does not
match.

## How a build happens

Actions → **Build engines** → Run workflow. It compiles each engine for
`macos-arm64` and `macos-x64`, verifies the architecture with `lipo`, ad-hoc
signs, smoke-tests that the signed binary answers `uci` and returns a
`bestmove`, then publishes one release per engine. The run summary lists each
asset's `sha256` and `sizeBytes` to paste into botvinnik's catalog.

Because the workflow runs in this repo, it publishes with the default
`GITHUB_TOKEN` — no personal access token is required.
