# Standard Engineering Report Template

This modular LaTeX template is derived from the standardized architecture used in Hossein Molhem's 5G phased-array engineering report. It preserves the preferred cover typography, configurable headers, independent front matter, centralized content menus, reusable engineering environments, code styles, appendices, bibliography support, and optional logo mechanism.

## Primary design principle

The template separates three responsibilities:

```text
setup/00_metadata.tex        user-editable report identity and options
main.tex                     stable document assembly and compilation flow
frontmatter/titlepage.tex    title-page layout and typography
```

For a normal project, users should edit `setup/00_metadata.tex` and project content files. They should not need to modify `main.tex` or `frontmatter/titlepage.tex`.

## Directory structure

```text
report-template/
├── README.md
├── main.tex
├── references.bib
├── setup/
│   ├── 00_metadata.tex
│   ├── 01_packages.tex
│   ├── 02_layout.tex
│   ├── 03_math_macros.tex
│   ├── 04_code_styles.tex
│   ├── 05_environments.tex
│   ├── 06_advanced_math.tex
│   ├── 07_DSP_Radar_macros.tex
│   └── 08_theme.tex
├── frontmatter/
│   ├── titlepage.tex
│   ├── objective.tex
│   └── abstract.tex
├── content/
│   ├── 00_report_body.tex
│   └── section files
├── appendices/
│   ├── 00_appendices.tex
│   └── appendix files
├── figures/
│   └── logo-placeholder.png
├── codes/
├── preview/
└── generated build files ignored by Git
```

## Quick start

1. Open `setup/00_metadata.tex`.
2. Replace the fictional report, course/project, institution, author, supervisor, and submission fields.
3. Set the optional title-page switches to `true` or `false`.
4. Replace the content of `frontmatter/objective.tex`.
5. Replace the content of `frontmatter/abstract.tex`.
6. Add, remove, or reorder sections in `content/00_report_body.tex`.
7. Add, remove, or reorder appendices in `appendices/00_appendices.tex`.
8. Replace fictional figures, code, tables, citations, and engineering results.
9. Compile `main.tex` and inspect the complete PDF.

## Metadata configuration

All project-specific report identity is centralized in:

```text
setup/00_metadata.tex
```

The file controls:

- course, program, client, or project context
- institution/company and department/business unit
- assignment type and report titles
- formatted cover title and plain PDF title
- author, student ID, supervisor, semester, and date
- page-header text
- cover-logo path and width
- PDF subject and keywords
- visibility of optional title-page fields

### Optional title-page switches

```latex
\showreportlogotrue
\showstudentidtrue
\showsupervisortrue
\showsemestertrue
```

Change `true` to `false` to suppress an item without deleting its metadata or modifying the title-page layout.

Example:

```latex
\showstudentidfalse
\showsupervisortrue
\showsemesterfalse
```

### Formatted and plain report titles

The cover-page title may contain manual line breaks:

```latex
\newcommand{\reporttitle}{%
    Technical Project Report:\\[0.35cm]
    Smart Sensor Array Design\\[0.15cm]
    and Validation%
}
```

The PDF title should remain plain text:

```latex
\newcommand{\reporttitleplain}{Technical Project Report: Smart Sensor Array Design and Validation}
```

Do not place layout commands in `\reporttitleplain`.

## Optional cover logo

The template includes an example logo asset at:

```text
figures/logo-placeholder.png
```

Configure it in `setup/00_metadata.tex`:

```latex
\showreportlogotrue
\newcommand{\reportlogo}{figures/logo-placeholder.png}
\newcommand{\reportlogowidth}{7.2cm}
```

When the logo is enabled but the configured file is missing, the title page deliberately displays a diagnostic box. This avoids silently producing a report without a required logo.

Use only logos for which publication or submission is authorized.

## Main document controller

`main.tex` is intentionally limited to:

- document-class selection
- metadata and setup-module loading
- PDF properties
- bibliography registration
- title-page and front-matter sequence
- table of contents and lists
- report-body menu
- appendix menu
- bibliography output

This keeps the document assembly stable across projects.

## Front matter

The `frontmatter/` directory contains independent components:

- `titlepage.tex` — layout only; reads all values from `setup/00_metadata.tex`
- `objective.tex` — project objective
- `abstract.tex` — technical abstract

The objective remains separate from the abstract because the two serve different functions in engineering reports.

## Running headers

Headers are defined through metadata:

```latex
\newcommand{\headerleft}{\courseshorttitle}
\newcommand{\headerright}{\reportshorttitle}
```

Both `fancy` and `plain` page styles use these values so chapter openings, contents pages, lists, appendices, and references retain consistent headers.

## Content menu

The report body is controlled from:

```text
content/00_report_body.tex
```

Example:

```latex
\input{content/01_introduction}
\input{content/02_theory}
\input{content/03_methodology}
\input{content/04_results}
\input{content/05_discussion}
\input{content/06_conclusion}
```

Commenting or reordering these lines changes the report structure without modifying `main.tex`.

## Appendices

Appendices are controlled from:

```text
appendices/00_appendices.tex
```

Example:

```latex
\input{appendices/appendix_a_source_code}
\input{appendices/appendix_b_console_output}
```

For a report with no appendices, comment the appendix calls in `main.tex`.

## Demonstrated capabilities

The fictional sample report exercises the template rather than claiming authoritative engineering results. It demonstrates:

- objective and abstract pages
- equations, aligned derivations, vectors, and matrices
- theorem, lemma, definition, example, and problem environments
- custom objective and engineering callout environments
- figures and subfigures
- standard tables and long tables
- TikZ diagrams
- Python, MATLAB, Vitis HLS, PowerShell, and console listings
- missing-figure placeholders
- appendices
- IEEE-style citations and bibliography

## Adding code

Place source files under `codes/` and include them with the listing styles defined in `setup/04_code_styles.tex`. Keep reusable style definitions in `setup/`; keep project-specific source code in `codes/`.

## Compilation

Preferred command:

```bash
latexmk -pdf main.tex
```

The template uses `biblatex` with Biber. Manual sequence:

```bash
pdflatex main.tex
biber main
pdflatex main.tex
pdflatex main.tex
```

Clean generated files:

```bash
latexmk -c
```

### Overleaf

Upload the complete `report-template` folder as a ZIP and set `main.tex` as the main document. Use pdfLaTeX as the compiler. Overleaf will invoke Biber when required by the bibliography configuration.

## Validation checklist

- [ ] `setup/00_metadata.tex` contains project-specific values
- [ ] Formatted and plain report titles are synchronized
- [ ] Optional title-page switches have the intended states
- [ ] Logo is present only when authorized and required
- [ ] Cover title and metadata are correct
- [ ] Objective and abstract are project-specific
- [ ] Headers display the intended short titles
- [ ] Table of contents and lists are current
- [ ] All figures and tables are referenced
- [ ] All citations resolve
- [ ] No placeholder content remains
- [ ] Appendix menu matches included files
- [ ] No confidential or proprietary material is present
- [ ] PDF properties show the intended title and author
- [ ] Final PDF has been visually inspected page by page

## Maintenance rule

Changes to the template itself must follow the repository branch, pull-request, changelog, and handoff workflow documented in [`../docs/WORKFLOW.md`](../docs/WORKFLOW.md).

---

## EE 6800 project-control addendum

This instance is no longer a generic sample report. It is the master technical-report
architecture for the EE 6800 PYNQ-Z2 SCM project.

Project-control rules:

1. The report architecture is frozen before experimental results are inserted.
2. Every major activity maps to a named gate and a focused pull request.
3. A gate closes only with theory/design basis, acceptance criteria, evidence, engineering interpretation, and an explicit decision.
4. PENDING sections remain visible; no placeholder is converted into a claim without evidence.
5. Repository evidence is authoritative for implementation status; report prose summarizes and interprets that evidence.
6. Negative results, timing misses, numerical limitations, and integration failures are retained and interpreted.
7. Each PR should form a vertical engineering slice: requirement/contract -> implementation/evaluation -> test/evidence -> report update.
