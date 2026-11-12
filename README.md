# Chromium Custom Build & Telemetry Canary

## Candidate
- **GitHub:** `dankscreamer`
- **Chromium commit:** `c52b7d226b4b4dce19add41cdb487d1d78f70dd0`
- **Build target:** `content_shell`
- **Demo video:** [ADD LOOM URL]

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

## Submission Conten# Chromium Canary Submission — Adit Singh (@dankscreamer)

## 1. Loom Walkthrough Video

- **Video URL:** [Watch 5-Minute Walkthrough](YOUR_LOOM_VIDEO_URL)
- **Sharing:** Anyone with the link can view.

## 2. Build Info

- **Chromium Commit ID:** `c52b7d226b4b4dce19add41cdb487d1d78f70dd0`
- **Target Built:** `content_shell`
- **Operating System:** macOS (Apple Silicon)
- **Hardware:** MacBook Pro M3 Pro
- **RAM:** 18 GB

## 3. Architecture Notes

### Feature A — `X-Candidate-Canary` Header

The Browser process receives the `--inject-candidate-id` command-line switch at startup. The switch is propagated to child processes, including the Utility process through `UtilityProcessHost::StartProcess()`, where the Network Service may run.

`HttpUtil::GetCandidateCanaryId()` reads and validates the switch value in the process handling the request. The value is then added to outgoing HTTP/1 requests in `HttpStreamParser::SendRequest()` and HTTP/2 requests in `CreateSpdyHeadersFromHttpRequest()`. If the switch is absent, empty, or invalid, the header is not added.

### Feature B — Blink Console Canary Hook

The hook is implemented in Blink's `FrameConsole::ReportMessageToClient()`, which handles console messages from the renderer. It checks for a Console API warning whose exact message is `canary-ping`.

When the condition matches, it emits a native C++ `LOG(INFO)` message containing `[CANARY_ACK]`, the GitHub username `dankscreamer`, and a runtime timestamp obtained using `base::TimeTicks::Now()`. Other console warnings continue through the normal reporting path.

## 4. AI Agent Workflow

- **AI Tool:** OpenAI Codex CLI
- **Model:** 6.1 Sol — High

### Sub-Agent Usage

I used three agents to divide the work:

1. **Feature A Agent:** Investigated the networking and command-line switch propagation requirements for injecting the candidate ID into outgoing requests.
2. **Feature B Agent:** Investigated Blink's console-message handling path and identified where the native C++ logging hook should be implemented.
3. **Main Coding Agent:** Coordinated the work, integrated the findings from both sub-agents, and implemented the changes across the relevant Chromium source files.

This separation helped keep codebase exploration focused on the two independent features while allowing the main agent to coordinate the implementation.
