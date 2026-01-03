# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Repository overview

This repository is a collection of Pandoc / LaTeX templates intended to be consumed from Obsidian (via plugins such as Obsidian Enhancing Export) or from plain Pandoc/LaTeX workflows. There is no application code, build system, or automated test suite: the main activity here is editing templates and compiling example documents to verify the output.

Top-level layout:
- `README.md`: explains how to install and use templates with Pandoc and Obsidian, and how to contribute new templates.
- `dissertation/` and `neurips/`: self-contained Pandoc templates (modified from Eisvogel and the NeurIPS 2023 template, respectively) with their own `README.md` files describing required YAML front matter.
- `kaobook/`: a vendored LaTeX class and examples for book/report-style documents.
- `img/`: image assets (e.g., the intro screenshot used in the root README).

## Key commands and workflows

There is no project-specific build, lint, or test command. Working with this repository is about compiling LaTeX/Pandoc examples to visually verify changes.

### General Pandoc / template usage

From the root `README.md`:
- Discover Pandocs user data directory:
  - Run in a terminal: `pandoc --version`
  - Look for the line starting with `User data directory:` (e.g. `/Users/leo/.local/share/pandoc`).
- Install a template into Pandoc:
  - If the user data directory does not exist yet, create it.
  - Copy the folder of the template you want (e.g. `dissertation/`, `neurips/`, or another template directory) into that user data directory.
- Generate preview images for examples (requires ImageMagick):
  - From inside an `example/` directory containing `example.pdf`:
    - `convert example.pdf +append -alpha remove -alpha off example.png`

After installation, you typically invoke templates via your Pandoc/Obsidian tooling (see the external Obsidian Tutorial for Academic Writing referenced in the root README for end-to-end setup).

### Dissertation template (Pandoc)

The `dissertation/` directory provides a Pandoc template configured via YAML front matter. Typical usage pattern:
- Create a Markdown file whose first lines are YAML front matter matching the example in `dissertation/README.md` (fields such as `title`, `abstract`, `author`, `acknowledgements`, `declaration`, `text1`-`text4`, `titlepage-logo`, `link-citations`, `reference-section-title`).
- Fields like `acknowledgements`, `abstract`, and `declaration` are optional: if omitted, the corresponding sections are removed from the output.
- Run Pandoc from your editor or command line using this template (exact invocation is usually managed by your Obsidian plugin or your own Pandoc command line).

### NeurIPS template (Pandoc)

The `neurips/` directory provides a template based on the 2023 NeurIPS style.
- Configure documents via YAML front matter as shown in `neurips/README.md`:
  - Top-level fields like `title`, `abstract`, and `reference-section-title`.
  - An `authors` array, where each entry contains `name`, `affiliation`, `institution`, `email`, and `address`.
- As with the dissertation template, you typically use this through Pandoc/Obsidian rather than running any repo-specific script.

### Kaobook: compiling examples and working locally

The `kaobook/` subtree is a full LaTeX class with its own documentation and examples. Common commands are derived from `kaobook/README.md`.

#### Compile the kaobook documentation example (Unix-like systems)

From the repository root:
- `cd kaobook`
- Compile the main documentation example (using `pdflatex` and `biber`):
  - `pdflatex -output-directory examples/documentation main.tex`
  - `biber -output-directory examples/documentation main`
  - `pdflatex -output-directory examples/documentation main.tex`
  - `pdflatex -output-directory examples/documentation main.tex`
  - `pdflatex -output-directory examples/documentation main.tex`
- Optionally compile glossary and nomenclature (from within `kaobook/examples/documentation`):
  - `makeindex main.nlo -s nomencl.ist -o main.nls`
  - `makeglossaries main`
- Then run `pdflatex` again as needed from the `kaobook` directory to stabilize all references.

This sequence is the closest equivalent to a build pipeline for the `kaobook` subproject.

#### Normal LaTeX workflow with kaobook

For a simple local workflow using the kaobook class (from `kaobook/instructions/normal_usage/README.md`):
- Ensure a TeX distribution is installed on your machine.
- In `kaobook/`, either:
  - Start from an existing example under `kaobook/examples/` (copy a `main.tex` you like to the root of `kaobook/`), or
  - Create a new `main.tex` whose first line is either `\\documentclass{kaobook}` (for books) or `\\documentclass{kaohandt}` (for reports).
- Compile from the command line:
  - `cd kaobook`
  - `pdflatex main.tex`
- Or use your preferred LaTeX editor to run the equivalent command.

#### TeXLive integration for kaobook

For integrating kaobook with a local TeXLive installation (from `kaobook/instructions/texlive_integration/README.md`):
- Find your personal TeX tree:
  - `kpsewhich -var-value=TEXMFHOME`
- Under that directory, create `tex/latex/kaobook/`.
- Copy the kaobook class and style files into that folder (or clone this repository there):
  - Files include `kaobook.cls`, `kaohandt.cls`, `kao.sty`, `kaobiblio.sty`, `kaorefs.sty`, `kaotheorems.sty`.
- Verify LaTeX can find the class:
  - `kpsewhich kaobook.cls`

Once integrated, any LaTeX project on your system can use `\\documentclass{kaobook}` without copying class files into the project directory.

## Architecture and structure

### Template convention

The root `README.md` defines the expected structure for each template directory (used for contributions and for plugins that assume a consistent layout):
- `TemplateName/`
  - `example/`
    - `TemplateName.md`  minimal Markdown example used to generate the sample output.
    - `TemplateName.pdf`  PDF output generated from the example.
    - `TemplateName.png`  preview image produced from the PDF (typically the first pages).
  - `TemplateName.tex`  main LaTeX template file.
  - `TemplateName.sty`  accompanying style file, if needed.
  - `README`  description of the template, including any configuration specifics.
  - `LICENSE`  licensing information for that template.

Not every template directory in this repository follows the naming `TemplateName` literally, but the structure and intent are the same: each template is self-contained and documents how to configure it via YAML front matter or LaTeX options.

### Dissertation and NeurIPS templates

The `dissertation/` and `neurips/` directories each contain:
- Core LaTeX/Pandoc template files (e.g. `dissertation.tex`, `neurips.tex` and associated style/resources).
- A `README.md` that:
  - Shows a sample YAML header you should paste at the top of your Markdown source.
  - Documents which fields are optional versus required, and how they affect the rendered output (e.g. optional sections like acknowledgements/abstract/declaration in the dissertation template; structured `authors` list in the NeurIPS template).
  - Links to preview PDFs (`example/*.pdf`) and PNGs where applicable.

Future modifications to these templates should respect the documented YAML interfaces, as Obsidian plugins or user workflows may rely on them.

### Kaobook subproject

The `kaobook/` directory is effectively an embedded third-party project (a LaTeX class and examples). Its internal structure (summarized from `kaobook/README.md`) is:
- Class files:
  - `kaobook.cls` (book-style documents).
  - `kaohandt.cls` (report/handout-style documents).
  - Shared style definitions in `kao.sty` and related `*.sty` files (e.g. bibliographic and theorem-related styles).
- `examples/`: several example projects (documentation, a minimal book, a machine learning report, etc.), each with its own `main.tex` and assets.
- `instructions/`: guidance on different workflows (Overleaf, self-hosted ShareLaTeX, TeXLive integration, and normal usage on a local machine).

You should treat `kaobook/` as an upstream dependency: when making non-trivial changes to the class or styles, consider mirroring upstream changes or documenting divergences in the relevant README files.

## Contributing new templates

The root `README.md` describes how to contribute additional templates via pull requests:
- Add a new directory following the template convention above (with `example/`, `README`, `LICENSE`, and the core `.tex` / `.sty` files).
- Ensure the `README` inside your template directory clearly documents:
  - What the template is for.
  - Any special YAML front matter or configuration required.
  - How to reproduce the example PDF from the provided Markdown.
- If you are integrating this repository into an Obsidian plugin, open a pull request so the plugin can be listed and tracked in `README.md`.

There are no repository-specific linting or testing requirements, but any substantial change should be validated by recompiling the affected examples and visually checking the output.