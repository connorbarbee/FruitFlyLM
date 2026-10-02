# Wilson conversation and feedback update

## What changed

- Qwen3 uses a bounded thinking phase (up to 384 generated tokens), followed by a separately sampled answer. Tool and review JSON is constrained only after thinking ends, and generation stops as soon as its JSON object is complete. Chat answers are separated from any `<think>` blocks. Thinking, when returned, appears in a collapsed **Thinking** dropdown. Incomplete thinking stays out of the answer. Literal tags in fenced code remain code. Only the answer enters subsequent chat context.
- Code responses use the same disclosure for a concise tool-step summary and any returned thinking. Existing tool permissions and offline execution checks are retained. Code mode now includes the contents of up to four explicitly named workspace files as context, through the existing guarded read path. Prompts emphasize relative paths, module imports for fresh Python processes, and the user's requested checks instead of blindly copying a generic test template.
- Each model response has thumbs-up/down controls. Click the other thumb to change a vote, or click the selected thumb to remove it. Votes are disabled during generation to avoid changing a controller used by the worker thread.
- The brain display adds layered optic lobes, cupped mushroom-body calyces, paired stalks and vertical/medial lobes, antennal lobes and separate dopamine-related clusters. This is a procedural fly-anatomy schematic driven by the existing controller, not a measured biological reconstruction.
- The **Dopamine** meter shows a simulated reward signal. Positive votes move it toward 95%; negative votes toward 5%. Recent votes carry more weight. The signal modestly affects the controller's exploration and persistence and the displayed modulatory clusters. Merely generating a chat answer does not count as positive user feedback.

## Primary model and local review helper

Wilson keeps the original **Huihui-Qwen3-1.7B-abliterated-v2 Q3_K_M** as its default. The requested **huihui-ai/Huihui-Qwen3-0.6B-abliterated-v2** is included as a **Q8_0 draft-review helper**, in `models/reviewer/Huihui-Qwen3-0.6B-abliterated-v2.Q8_0.gguf`. It is loaded automatically alongside the selected primary model. The model tooltip and Code mode log show when the helper is active. Both models run entirely locally.

The primary model produces a draft. The 0.6B helper returns a small, grammar-constrained review identifying a definite correctness, requirement, evidence, or conversational issue, or reports that none is visible. When an issue is identified, the primary model gets one revision attempt. The original system instructions, Wilson persona, action grammar, cancellation handling, workspace restrictions and tool approval checks remain in force. The helper never executes tools. Straightforward file inspection actions skip review. Oversized drafts/contexts skip review; malformed critiques or failed revisions preserve the original primary response.

This is a conservative local review step, not a guarantee that a smaller reviewer will catch every defect. It adds inference time and model memory use. It does not replace or modify the primary model weights. If the helper file is removed while the application is closed, the original primary-only inference remains available.

The helper was obtained using the user-authorized `hf download mradermacher/Huihui-Qwen3-0.6B-abliterated-v2-GGUF` command, restricted to the Q8_0 GGUF. Its SHA256 matches Hugging Face's download metadata:

`41e88e1aa2e8b8ccafcf5ca12449f8a8a59767b82084f171e7ac0bedcdc6df8e`

Repository revision: `a81ddae27343c9bfacfcd5e6252eb8b3ed41ed43`. File size: 639,443,552 bytes. The GGUF metadata identifies Qwen3, size 0.6B, the requested Huihui v2 source, and Apache-2.0 licensing. Attribution and provenance are recorded in `THIRD_PARTY.md` and `models/reviewer/model-info.json`.

## Local self-improvement loop

This update implements its own bounded response-strategy learning loop; it does not bundle Hermes or claim autonomous model-weight training.

1. Classify a request as conversation, explanation, coding, or debugging.
2. Select one of three fixed response strategies using ratings from that category and model. The score is the sum of votes divided by the number of votes plus two, which limits the influence of sparse feedback. Untried strategies have neutral scores; disliked strategies yield to alternatives.
3. Generate a response with the selected strategy, review it with the local 0.6B helper, and let the primary model revise once when a concrete issue is identified. Code mode retains the existing tool execution, observation and repair loop.
4. Associate the user's vote with that response's strategy. Save the vote atomically, recompute dopamine and use the updated scores for later requests.

The memory is limited to 512 rated responses, survives restart and new chats, and separates statistics by model and task category. It stores only random response IDs, fixed category/strategy IDs, a hash identifying the model filename, and votes. It does not save questions, answers, reasoning, code, file paths, timestamps, user IDs or machine IDs. No generated text becomes an executable instruction or permanent system prompt.

The file is `data/wilson-feedback.json`, created on the first rating. To reset learning, close the app and delete that file. The release and source-control exclusions keep personal ratings out of redistributed packages. A failed save reports an error and leaves the prior learning state intact.

This can improve strategy selection from explicit feedback. It does not guarantee that a small local model becomes correct, or gain capabilities absent from its weights.

## Validation performed

Final validation results are summarized in `build-validation.txt` at the application root. Checks are limited to the changed thinking/rating/review paths, local model integration, packaged UI and release privacy.

The temporary official Hugging Face CLI was installed with user approval and used only for the authorized model download. Hub telemetry and implicit account tokens were disabled. The CLI, downloader dependencies and Hugging Face cache are not packaged with Wilson. The application has no networking, model download, remote service, telemetry or analytics additions. Existing shell/Python tools retain their original limitations: application checks are not an operating-system sandbox for arbitrary adversarial code.


## September 25: local chats, display and chat adapter

The header's plus button creates another chat; the dropdown opens saved chats. Chat and Code have separate lists and prompt histories. Messages, collapsed thinking, drafts and ratings are stored in `data/chats.json` next to the app and restored on restart. This file is private local conversation data, with no upload or sync; it is excluded from release packages. Close the app and remove it to clear history. Ratings remain separately in `data/wilson-feedback.json`.

Only Activity and Dopamine meters remain visible. The procedural schematic now includes translucent lobe outlines and fine bifurcating arbors (6,098 points and 6,707 connections). Geometry is built once, active animation is capped at 15 FPS, hidden views skip rendering, and idle animation stops. It is illustrative anatomy, not a reconstructed biological connectome.

Thumbs down means repetitive or poor. It lowers simulated dopamine, changes future strategy selection, and omits that response from subsequent Chat prompt context. The visible history remains intact. Votes can be changed or removed and persist with the response when switching chats. This is strategy adaptation, not weight training.

An experimental adapter derived from [bunnycore/Qwen3-1.7B-abliterated-lora](https://huggingface.co/bunnycore/Qwen3-1.7B-abliterated-lora) is included. Its model card says it was extracted from mlabonne/Qwen3-1.7B-abliterated against Qwen/Qwen3-1.7B. The downloaded source hash was verified against Hub metadata. The local GGUF contains all 392 projection A/B tensors in F16. Full embedding, output and normalization replacements are omitted to preserve the current Huihui model; this is explicitly a projection-only adaptation, not a faithful conversion of the complete PEFT checkpoint. See `models/lora/model-info.json` for provenance and hashes.

The adapter loads only for the named Huihui Qwen3 1.7B v2 base and applies at 0.25 strength in Chat, never Code or the review helper. Settings includes an experimental-LoRA toggle for the current loaded model; reloading restores the default. Removing `models/lora` while closed disables it permanently. The original default GGUF is unchanged. Loading/inference works, but neither its model card nor our single live reply proves a conversational quality improvement on the already-abliterated Huihui base. Disable it if replies worsen.

The new standalone ZIP includes runtime DLLs, Qt plugins, Python and both models. Extract the entire folder before launching FruitflyLM.exe. Do not run the executable directly inside the ZIP.


## 0.4.0 maintenance release

Original local fly artwork is embedded as the Windows EXE icon, application icon and header logo. The header and chat selector have consistent themes, focus borders and a narrower layout. Ctrl+N creates a new conversation; Escape cancels work; right-click the selector to rename or delete a saved conversation.

Chat archives now validate conversation/message structure before loading, preserve malformed files, cap saves at 32 MB and render only the most recent 200 messages per conversation while retaining older saved content. Windows prevents concurrent interactive instances for the same installation to avoid lost history updates. Changing workspace while in Code starts a fresh task.

The coding loop blocks a finish action after file edits until a subsequent execution succeeds. This is a guard against unexecuted claims, not proof that a model-generated test is meaningful. The single live coding run exposed a startup regression that substituted the generic task for an explicit smoke-test task; the startup ordering is now fixed and covered by a focused regression check. See RELEASE-0.4.md for the actual live-run outcome.

No network, telemetry, account integration or remote image-generation service was used for this maintenance work. Release artifacts omit private histories, feedback, workspaces, caches and machine-specific build logs. Source and standalone packages are provided separately.
