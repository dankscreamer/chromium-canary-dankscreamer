# Chromium Custom Build & Telemetry Canary

## Candidate
- **GitHub:** `dankscreamer`
- **Chromium commit:** `c52b7d226b4b4dce19add41cdb487d1d78f70dd0`
- **Build target:** `content_shell`
- **Demo video:** https://youtu.be/QuGIj5vHCKs

## Environment
- **Operating system:** macOS
- **Hardware:** MacBook Pro, Apple M3 Pro
- **Memory:** 18 GB RAM
- **Architecture:** Apple Silicon (ARM64)

## Implementation

### Feature A — Candidate Canary Header
Added the `--inject-candidate-id` command-line switch to inject an `X-Candidate-Canary` request header.

The implementation covers:
- HTTP/1.1 request serialization.
- HTTP/2 request header conversion.
- Propagation of the command-line switch to renderer and utility processes.

HTTP/3 is outside the implementation scope.

### Feature B — Console Canary Acknowledgement
Added a targeted acknowledgement in Blink's frame console handling.

When the page emits the exact warning:

`console.warn("canary-ping")`

Chromium writes an INFO log containing:

`[CANARY_ACK] Verified build for dankscreamer at <timestamp>`

Ordinary warnings do not trigger this acknowledgement.

## Build and Verification

- Successfully built `content_shell` using `autoninja`.
- Verified the candidate header using `https://httpbin.org/headers`.
- Verified the HTTP/2 request path.
- Verified the console acknowledgement using DevTools.
- Verified that an ordinary warning does not trigger the acknowledgement.

Build command:

```bash
autoninja -C out/Default content_shell
```

Launch command:

```bash
./out/Default/Content\ Shell.app/Contents/MacOS/Content\ Shell \
  --inject-candidate-id="dankscreamer" \
  --enable-logging=stderr \
  https://httpbin.org/headers
```

## GN Configuration

See [`args.gn`](args.gn) for the build configuration.

The `enable_nacl` argument from the assignment was not included because it is not recognized by this Chromium checkout.

## AI Tooling and Workflow

- **Tool:** Codex CLI
- **Model:** [ADD EXACT MODEL NAME]
- **Workflow:** Used two research sub-agents and one main coding agent. Reviewed implementation suggestions, inspected Chromium source paths, and performed build and runtime verification.
- **Transcripts:** Raw Codex session exports are included in `ai_transcripts/`.
