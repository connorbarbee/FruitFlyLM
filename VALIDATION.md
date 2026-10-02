# FruitFlyLM 0.3.0 validation

The Windows x64 Release build contains the native C++ desktop application, local GGUF inference, the command-line runner, and the Feather converter. The portable directory supplies Qt Core/Gui/Widgets and the separate Python/SymPy tool runtime.

Completed release checks:

- Native application, CLI and converter compiled successfully with local dependencies and compiler source-path mapping.
- Native test suite: 29 passed, 0 failed, 0 skipped, including offline tool guards and a successful local Python unit-test workflow.
- Real local-model check with Qwen2.5-Coder-14B-Instruct GGUF: generated a calculator with addition, subtraction, multiplication, division and division-by-zero handling. Its generated suite passed 4 test methods with 13 assertions; 8 separately checked cases also passed.
- Complete real-model agent workflow: generated `arithmetic.py` and three unit tests, invoked the bundled Python runner, received three passing tests with exit code 0, and returned a successful finish action. No generated code was manually repaired.
- The redesigned GUI completed the four-step preview workflow successfully. Its rendered screen was inspected, including the conversation, composer, and interactive map with 2,476 illustrative points and 3,195 display connections.
- The staged converter self-test passed: Feather read/write, annotation roles, filtering, normalization and graph output.
- The staged portable Python runtime imported SymPy and factored a polynomial correctly without relying on a system Python installation.
- The initial clean portable-directory scan covered 1,736 files and found no configured local username/profile identifiers in UTF-8 or UTF-16 content. Qt Network, networking plugins, QTest, compiler debug files, generated Python bytecode caches and development logs were absent.
- The project source scan found no configured personal identifiers or excluded release files. Upstream license and copyright notices are retained.

The updated interface uses a quiet conversation view and a detailed bilateral Brain Map. The map is an illustrative anatomical layout driven by controller activity; it does not claim to display measured neurons or synapses. The bundled graph remains synthetic.

These checks describe the supplied build, not a general model-quality benchmark. Coding behavior depends on the selected local model and task. Shell/Python safeguards are not an operating-system sandbox; see `PRIVACY.md`.
