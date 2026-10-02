# Security and local data

No network dependencies or telemetry are enabled in the application build. Offline checks reject known network commands and remote paths; these application checks do not isolate arbitrary Python or native programs at the OS level. For untrusted workspaces, use an OS-isolated account or virtual machine with network access disabled. Run with standard-user privileges.

Chat content is user-controlled private data, stored locally without encryption. Protect the installation directory with OS file permissions. Close the app before moving or backing up its data. Never include chat, feedback, workspace, downloaded caches or local build logs in public releases. The release scripts exclude these files, and privacy scanning checks the staging directory.

The bundled models can hallucinate and generate insecure or incorrect code. Review diffs before adopting generated changes. A successful execution result is evidence of that execution, not a guarantee of correctness or safety. The experimental LoRA is optional; its effectiveness is not established.

Dependency versions and model provenance are recorded in the build instructions and third-party notices. This release has no automatic update mechanism, remote reporting, or certificate/signing service. No external security certification is implied.
