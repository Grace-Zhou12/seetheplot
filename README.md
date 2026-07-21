# SEETHEPLOT

SEETHEPLOT is a VS Code visual layout editor for Matplotlib figures. It runs a
Python plotting file, shows the generated image inside VS Code, and places an
interactive layer over source-locatable layout elements. Drag an element or
edit its properties, review the source diff, and apply the change back to the
Python file.

## Features

- Full generated Matplotlib image and structure mode preview.
- Drag titles, axis labels, text, annotations, and legends.
- Edit text, font size, rotation, color, title/label alignment, and layout.
- Batch changes in memory; Python is not re-run during dragging.
- Review a diff before applying source changes.
- Format-preserving source edits through LibCST.
- VS Code Undo/Redo through native `WorkspaceEdit`.
- Seaborn plots are supported when layout calls are explicit Matplotlib calls.
- Python core is bundled into the VSIX and can also run as a standalone CLI.

## Quick start

The repository contains a working development interpreter at `core/.venv`.
If PyPI is unavailable, use that interpreter instead of reinstalling the
dependencies:

```bash
"$PWD/core/.venv/bin/python3" -c "import matplotlib, numpy, libcst; print('SEETHEPLOT runtime OK')"
code --install-extension "$PWD/extension/seetheplot-0.1.0.vsix" --force
```

For a fresh environment, install the runtime dependencies:

```bash
python -m pip install matplotlib numpy libcst
```

Open the workspace and `demo_plot.py`, then run the command:

```text
SEETHEPLOT: Open Preview
```

## Development

```bash
cd core
python -m pip install -e .
UV_CACHE_DIR=/tmp/seetheplot-uv-cache uv run pytest

cd ../extension
npm install
npm run build
npm run package
```

The packaged extension is written to:

```text
extension/seetheplot-0.1.0.vsix
```

## Core CLI

```bash
seetheplot run plot.py --mode full
seetheplot run plot.py --mode skeleton
seetheplot patch plot.py --manifest manifest.json --operations operations.json
```

The core emits a versioned JSON manifest with figure assets, element bounds,
source references, editable properties, diagnostics, and a source hash. A
patch is refused when the source changed after analysis.

## Supported v1 source calls

The reliable write-back subset includes `set_title`, `set_xlabel`,
`set_ylabel`, `text`, `annotate`, `legend`, tick-label APIs,
`figure(figsize=...)`, and `set_size_inches(...)`. Dynamic expressions or
elements without a unique source location are shown as read-only.

## Project layout

```text
core/       Python renderer, instrumentation, manifest, cache, and patcher
extension/  VS Code extension, React Webview, and VSIX build scripts
demo_plot.py
```

## License

MIT License. See [LICENSE](LICENSE).
