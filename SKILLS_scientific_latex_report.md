---
name: scientific-latex-report
description: >-
  Standardized scientific LaTeX reporting, templates, and compilation workflows for academic and technical
  PDF generation. Use this skill when drafting research reports, technical evaluations, paper manuscripts,
  or compiling publication-ready PDFs from LaTeX sources.
---

# Scientific LaTeX Report – Guidelines & Compilation Workflows

This skill specifies the LaTeX architecture, typography guidelines, math conventions, figure embedding, and compilation protocols for producing publication-quality scientific reports and white papers.

---

## 1. Document Architecture & Recommended Preamble

All scientific reports should reside in a `report/` or `docs/` subfolder and follow a clean, modular structure:

```latex
\documentclass[11pt,a4paper]{article}
\usepackage[utf8]{inputenc}
\usepackage[margin=2.0cm]{geometry}
\usepackage{graphicx}
\usepackage{amsmath,amssymb}
\usepackage{booktabs}
\usepackage{hyperref}
\usepackage{caption}
\usepackage{subcaption}
\usepackage{float}
\usepackage{xcolor}

\hypersetup{
    colorlinks=true,
    linkcolor=blue,
    citecolor=blue,
    urlcolor=blue
}

\title{\textbf{Title: Specific, Descriptive, and Informative}}
\author{\textbf{Nils M. K. \and Research Collaborators}}
\date{\today}

\begin{document}
\maketitle

\begin{abstract}
Concise summary describing: (1) Background and objective, (2) Methodology and numerical models, (3) Quantitative key findings with effect sizes/metrics, and (4) Practical and scientific implications.
\end{abstract}

% Sections
\end{document}
```

---

## 2. Scientific Typography & Table Rules

1. **Booktabs Tables (No Vertical Rules)**:
   - Never use vertical lines (`|`) in scientific tables.
   - Use `\toprule`, `\midrule`, and `\bottomrule` from the `booktabs` package.
   - Right-align numeric values or align on decimal points.
   - Example:
     ```latex
     \begin{table}[H]
     \centering
     \caption{Evaluation of model skill against tide gauge observations.}
     \label{tab:model_skill}
     \begin{tabular}{lcccc}
     \toprule
     \textbf{Station} & \textbf{Correlation ($r$)} & \textbf{RMSE (m)} & \textbf{Bias (m)} & \textbf{Vector Diff (m)} \\
     \midrule
     Bergen (BGO)     & 0.96 & 0.12 & -0.01 & 0.04 \\
     Tregde (TRG)     & 0.94 & 0.09 & +0.02 & 0.03 \\
     Vardø (VAW)      & 0.92 & 0.22 & -0.04 & 0.08 \\
     \bottomrule
     \end{tabular}
     \end{table}
     ```

2. **Mathematical Precision**:
   - Variables must be italicized in math mode ($h, \zeta, u, v$).
   - Multi-letter abbreviations, operators, and subscripts that are words must be romanized with `\mathrm{...}` or `\text{...}`:
     $$\zeta_{\mathrm{int}} = \zeta_{\mathrm{TW}} - (\zeta_{\mathrm{TO}} + \zeta_{\mathrm{SO}})$$
     $$\mathrm{RMSE} = \sqrt{\frac{1}{N} \sum_{i=1}^N (\zeta_{\mathrm{mod}, i} - \zeta_{\mathrm{obs}, i})^2}$$
   - Units must be upright with a thin space (`\,`): `$18.3$\,cm`, `$1.5$\,m\,s$^{-1}$`.

3. **High-Resolution Figures**:
   - Save figures as vector PDF or 300 dpi PNG files in a dedicated figures folder (`../figures/` or `figures/`).
   - Use `\textwidth` scaling to ensure readable font sizing:
     ```latex
     \begin{figure}[H]
     \centering
     \includegraphics[width=0.95\textwidth]{../figures/fig1_timeseries_overview.png}
     \caption{Time-series comparison of observed total water vs coupled simulation during the January 1990 storm.}
     \label{fig:storm_1990}
     \end{figure}
     ```

---

## 3. Automated Compilation Script

Never run interactive or ad-hoc LaTeX calls that leave broken logs. Use a standardized, two-pass non-interactive shell script or Makefile to resolve references and table of contents:

```bash
#!/usr/bin/env bash
set -euo pipefail

REPORT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
cd "${REPORT_DIR}"

MAIN_TEX="evaluation_report.tex"
PDF_NAME="${MAIN_TEX%.tex}.pdf"

echo "=== Compiling ${MAIN_TEX} (Pass 1) ==="
pdflatex -interaction=nonstopmode -halt-on-error "${MAIN_TEX}"

echo "=== Compiling ${MAIN_TEX} (Pass 2 for refs/citations) ==="
pdflatex -interaction=nonstopmode -halt-on-error "${MAIN_TEX}"

echo "=== Cleaning auxiliary LaTeX artifacts ==="
rm -f *.aux *.log *.out *.toc *.fls *.fdb_latexmk *.synctex.gz

echo "=== Build Complete: ${REPORT_DIR}/${PDF_NAME} ==="
```

---

## 4. Quality Checklist for Deliverables

Before finalizing a PDF deliverable:
- [ ] No `?` or missing reference placeholders (`\ref`, `\cite`).
- [ ] No overfull `\hbox` warnings protruding into document margins.
- [ ] Figures have descriptive, standalone captions explaining what is plotted and what color lines mean.
- [ ] Tables include units in the column headers.
- [ ] Temporary auxiliary files (`.aux`, `.log`, `.out`) are cleaned up and excluded in `.gitignore`.
