# Wilson 0.4.0 release validation

## Delivered

- Original fly icon embedded in the EXE and window, with multi-size ICO, PNG master and local drawing source.
- Theme-matched conversation selector, keyboard focus, Ctrl+N, Escape, rename/delete controls and compact-window layout.
- Atomic bounded chat saves, schema validation, preservation of malformed history, 200-message rendering limit and Windows duplicate-instance guard.
- Fresh Code task on workspace change; explicit startup task preserved across Code-mode activation.
- Completion blocked when files changed after the last successful execution action.
- Honest offline/build/security documentation and a local dependency inventory. No networking, telemetry or remote image service used in this maintenance turn.

## Checks

Final build succeeded without warnings. GUI suite: 4 passed, 0 failed, covering history validation/preservation and existing chat/rating/mode behavior. After discovering the startup issue, its targeted regression passed (3 including setup/cleanup). Six selected core behavior checks passed across the focused run and the corrected offline-policy rerun. The offline-policy test initially selected an unavailable system Python; rerunning only that check with the bundled interpreter passed. No second real-model coding test was run.

The standalone app launched at 960x640 with system-only PATH and bundled DLLs/plugins, exit 0. Both themes were visually reviewed. Windows extracted the new icon and reported FileVersion 0.4.0. Release staging excludes user chat/feedback files, workspaces, caches and local build logs; the privacy scan passed.

## Exactly one live coding attempt: FAILED

The requested temperature-conversion task was cleared by a startup mode-switch bug, so Wilson received the fallback task to create and verify a small program. It generated add_numbers.py, encountered import/syntax errors, attempted repairs and reached 12 steps without successful completion. The final file was empty. GUI exit was 1 and report ok=false. The startup bug is fixed and unit-checked, but was not followed by another live attempt in accordance with the one-run limit. The detailed local test transcript is deliberately excluded from public source staging.

This test does not prove autonomous coding capability. The application improvements are delivered; reliable model-generated coding and enterprise certification are not established. The existing small models and optional LoRA retain their documented limitations. The completion guard checks execution ordering, not the semantic adequacy of model-generated tests.
