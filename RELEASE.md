# Release procedure

1. Prepare the pinned dependency versions in `THIRD_PARTY.md` in local SDK directories. No network access is required during configuration, build, test or execution.
2. Build the native application, CLI and converter in Release mode. Run the native tests, the converter self-test, and a coding task using a local GGUF model. Verify that the generated code runs and produces its expected result; preview output alone does not count as model validation.
3. Inspect the main window, map and settings at normal and compact sizes. Confirm that an ordinary message, task completion, cancellation, code output and failure are visible without losing access to detailed inspection.
4. Deploy matching Qt DLLs and plugins into a runtime directory. Include the official MSVC runtime, Arrow if the converter is supplied, and the separate portable Python/SymPy runtime if those tools are supplied. Retain applicable license files.
5. Stage into a new directory using the script below. The script intentionally refuses an existing output directory to avoid deleting or merging unrelated files.

```powershell
.\scripts\Stage-Release.ps1 -BuildDir .\build -RuntimeDir .\runtime -LicenseDir .\licenses -OutputDir .\release\FruitFlyLM
.\scripts\Test-ReleasePrivacy.ps1 -Root .\release\FruitFlyLM
```

The staging script includes only the three native release executables, necessary Qt Widgets and image/platform/style plugins, runtime libraries, optional Python tools, bundled data, documentation and licenses. It excludes Qt Network, networking plugins, QTest, PDBs, development logs, Python cache directories and loose `.pyc` files. It never changes the runtime source directory. Copy only clean project source, documentation and scripts into a separate source archive; exclude build directories, dependencies, models and local logs.

6. Launch the staged application, repeat the coding check from that directory, and rerun the privacy scan afterwards. Exclude any newly generated test files or caches from the final package. Scan the source archive too, supplying any additional private identifiers through `-ForbiddenText` rather than committing them to the script.
7. Package the portable directory. Keep the executable and its companion folders together. Publish only sanitized validation summaries; retain detailed logs locally. Document the exact model and validation result without local paths or personal information.

The privacy scan detects configured literal identifiers, not every possible kind of personal information. It is one release check, alongside reviewing fixtures and the staged file list.
