---
name: academic-presentations
description: >-
  Communication-first standards, narrative structure, design rules, and multi-format implementation
  workflows for academic and research presentations across LaTeX Beamer, PowerPoint (.pptx), Quarto,
  Typst, Reveal.js, and Marp. Use this skill when planning, drafting, building, or reviewing
  conference talks, seminar slides, thesis defenses, grant briefings, lab meetings, or invited
  lectures in any slide format.
---

# Academic Research Presentations – Multi-Format Standards & Workflows

This skill establishes communication-first standards for academic and analytical presentations across **all slide engines** (LaTeX Beamer, PowerPoint/PPTX, Quarto, Typst, Reveal.js, Marp, and Google Slides). Adapted and extended from Gabberflast's `academic-pptx-skill`, Barbara Minto's *Pyramid Principle*, and Naegle (2021), it decouples **argument architecture and slide discipline** from **format-specific rendering**.

---

## Quick Workflow Overview

1. **Choose Mode & Format:** Default to **Structured Argument** mode; confirm target format (Beamer, Quarto, PPTX, Typst, or Web/Marp) and aspect ratio (`16:9` default).
2. **Outline & Ghost Deck Test:** Draft a slide-by-slide outline (role, action title, exhibit, citation) before writing slide code. Verify that action titles alone tell the full story. Confirm with the user if the deck exceeds 10 slides or covers complex material.
3. **Apply Design & Content Discipline:** Enforce white content backgrounds, single sans-serif typography, ≤ 3 colors, ~40-word body ceiling, 1 exhibit per results slide, direct chart annotations, and full attribution.
4. **Build in Target Format:** Apply the format-specific pattern from Section 4.
5. **Compile, Render & Visually QA:** Convert output to PDF/PNG and inspect every slide for text overflow, clipped labels, font readability, and adherence to the Academic QA Checklist (Section 5).

---

## Step 1: Select Presentation Mode & Format

### Presentation Mode

| Mode | When to Use | Priority Order |
| :--- | :--- | :--- |
| **Structured Argument** *(default)* | Conference papers, seminar talks, thesis defenses, grant briefings, lab meetings, invited research lectures, policy briefings | Argument structure → Data/exhibits → Layout → Aesthetics |
| **Visual / Narrative** | Public engagement talks, lay science communication, general keynotes | Narrative arc & visual clarity → Argument structure → Aesthetics |

When the user mentions a paper, dataset, model, thesis, grant, or conference, always use **Structured Argument**.

### Format Selection Guide (If Unspecified)

| Format | Best Fit | Key Strengths |
| :--- | :--- | :--- |
| **LaTeX Beamer** (`16:9`) | Math, physics, CS, economics, oceanography, STEM conferences | Native equations, `booktabs`, TikZ figure overlays, BibTeX/Biber, reproducible PDF |
| **Quarto** (`.qmd`) | Data science, R/Python workflows, computational notebooks | Single source → `revealjs`, `beamer`, or `pptx`; inline executable plots |
| **PowerPoint** (`.pptx`) | Collaborative institutional decks, grant panels, cross-disciplinary talks | Universal editing via `python-pptx`, `PptxGenJS`, or institutional `.potx` templates |
| **Typst** (`presentation-16-9` / `touying`) | Fast STEM authoring, modern LaTeX alternative | Sub-second PDF builds, clean grid layout, native math |
| **Marp / Reveal.js** | Live code/HTML demos, interactive Plotly/web visuals | Pure Markdown + CSS, browser presentation, PDF export |

---

## 1. Argument Structure & Deck Architecture

### Narrative Spines

Lead with the central research question or claim by **Slide 2 or 3** at the latest. Choose one narrative spine and apply it consistently:

- **Option A — Situation / Complication / Resolution (SCR):**
  1. *Situation:* What is established in the literature.
  2. *Complication:* What is missing, broken, contested, or unexplained.
  3. *Resolution:* What your study contributes.
- **Option B — Funnel + Answer:** Broad context → specific gap → approach → key findings → implications.
- **Option C — Answer First** *(grant panels, advisory boards, short talks)*: State the core conclusion upfront, then present supporting evidence.

### Structural Discipline

- **One argument per talk:** Do not present the entire paper. Select the single claim that can be defended convincingly in the allotted time; move secondary analyses to the Appendix.
- **One job per slide:** If a slide makes two distinct points, split it.
- **Flow test:** Read proposed slide titles in order. Each slide must feel like a logical consequence of the previous one.

### Required Slide Sequence

1. **Title Slide:** Full title framed as a claim or question (not just a topic), authors, affiliations, venue/conference, and date.
2. **Motivation / Context (1–2 slides):** Situation and complication—why the problem matters and what gap exists.
3. **Research Question / Hypothesis (1 standalone slide):** State the question or objective explicitly on its own slide so the audience can anchor all subsequent evidence.
4. **Methods / Experimental Setup (1–2 slides):** Only what the audience needs to evaluate your results (identification strategy, model physics, dataset, sample). Move granular parameters and derivations to the Appendix.
5. **Results (1 slide per key finding):** One exhibit per slide, action title stating the takeaway, key finding annotated directly on the figure/table.
6. **Discussion / Implications (1–2 slides):** Interpret findings relative to the opening question and **explicitly address main limitations** before the audience raises them.
7. **Conclusions (1 slide — final main slide):** 2–4 numbered takeaways plus contact email, paper/repo URL, or QR code. **This slide stays on screen during Q&A. NEVER end on a "Thank You" slide or a blank screen.**
8. **References:** Complete citations for every source cited in the deck.
9. **Appendix (clearly labeled, uncounted):** Pre-built Q&A slides anticipating likely objections, robustness checks, extra tables, and detailed derivations (e.g., *Appendix A — Sensitivity Analysis*).

### Timing & Slide Budgets

Target **≤ 1 main slide per minute** and rehearse to finish **1–2 minutes under** the time limit:

| Slot Length | Main Content Slides (excl. Title & Refs) | Compression / Backup Strategy |
| :--- | :--- | :--- |
| **10 min** | 8–10 slides | 1 motivation, 1 RQ, 1 methods, 3–4 results, 1 conclusions |
| **15 min** | 12–14 slides | Pre-mark 2–3 skippable detail slides in case Q&A/intro runs long |
| **20 min** | 15–18 slides | Add section dividers or subtle progress bar |
| **45 min seminar** | 30–40 slides | Include breadcrumb bar and 2–3 structured discussion pauses |

Prepare **3–5 Appendix slides** anticipating the top likely Q&A questions (e.g., alternative methods, boundary conditions, external validity, sensitivity checks).

---

## 2. Action Titles, Exhibit Discipline & Text Standards

### Action Titles (Non-Negotiable)

Every content slide title must be a **complete sentence stating the takeaway ("so what")**, never a generic topic label. Keep titles to **1–2 lines**.

| Avoid (Topic Label) | Use (Action Title) |
| :--- | :--- |
| *Results* | *Coupled tides reduce extreme surge residuals by 18% along the west coast* |
| *Literature Review* | *Prior barotropic models omit non-linear tide–surge phase shifts* |
| *Data* | *Analysis spans 30 years of hourly observations across 29 tide gauges* |
| *Methodology* | *Regression discontinuity exploits a sharp eligibility threshold at 185% FPL* |
| *Discussion* | *Inverse barometer under-response persists even after boundary tuning* |

**Ghost Deck Test:** Read only the slide titles from start to finish. If they do not form a coherent, complete summary of your argument, revise the titles or reorder the slides before building exhibits.

### Exhibit Discipline

- **One exhibit per results slide:** One chart, diagram, map, equation block, or compact table per slide.
- **Two-part validation:**
  1. *Cover the exhibit, read the title:* Does the title still make complete sense without the exhibit? If yes, the exhibit may be redundant.
  2. *Cover the title, inspect the exhibit:* Is the main pattern immediately obvious? If not, add direct annotations.
- **Annotate the key finding directly on the visual:** Use a callout box, arrow, shaded region, or focal highlight color (e.g., `↑ 28% in Q1`, `Peak error: 14 cm`) so back-row viewers never have to hunt.
- **Self-sufficient slide test:** Slides are frequently circulated as PDFs/handouts; key conclusions must remain clear without spoken narration.
- **Left-to-right reading order:** Place the **exhibit on the left (~55–60% width)** and **interpretive bullets on the right (~35–40% width)**.
- **Graphs over tables:** Use plots for trends, distributions, and comparisons; reserve tables for exact numerical benchmarks (formatted with `booktabs` style—no vertical lines).
- **Rebuild paper figures for projection:** Never paste tiny journal figures with 7 pt axis labels or multi-sentence PDF captions. Re-export at high resolution (vector PDF/SVG or 300 dpi PNG) with large, legible axis labels (≥ 16 pt at screen scale).
- **Discuss what you show:** Every element on screen must be verbally addressed; anything omitted belongs in the Appendix.

### Text Discipline

- **Word ceiling:** Keep body text to **~40 words per content slide** (excluding title and bottom citation).
- **Bullets are orientation cues:** 3–5 bullets per slide, one idea per bullet, telegraphic phrasing acceptable (*"Mean RMSE drops from 0.22 m to 0.12 m (p < 0.01)"* rather than full prose paragraphs).
- **Use bold/italics sparingly:** Bold key terms or focal numbers; reserve italics for mathematical variables, statistical symbols, species names, or publication titles.

### Citations, Integrity & Accessibility

- **On-slide attribution:** Cite borrowed claims, datasets, and figures at the bottom of the slide in a muted smaller font (`(Author, Year)` or `[1]`; for figures: `Source: Jones et al., 2021, Fig. 3`). Introduce sources orally as well.
- **References slide:** Always include a complete References slide before the Appendix.
- **AI transparency (when applicable):** If AI tools assisted with analysis, figure generation, or drafting, note it transparently on an acknowledgment/disclosure block or in speaker notes per venue norms.
- **Accessibility & international audiences:** High foreground/background contrast; never rely on color alone (combine color with direct labels, markers, or dashed/solid styles); define acronyms on first use; avoid idioms or culturally specific humor.

---

## 3. Communication-First Design Tokens

Academic slides prioritize **analytical clarity over decoration**. Apply these defaults unless an institutional template dictates otherwise:

### Color Palette (Maximum 3 Functional Colors)

| Role | Hex | Usage |
| :--- | :--- | :--- |
| **Background (Content)** | `#FFFFFF` | All content, methods, results, and reference slides |
| **Primary (Dark Navy)** | `#1F4E79` | Action titles; optional dark background for Title, Section Dividers, and Conclusions ("sandwich" framing) |
| **Accent (Mid-Blue)** | `#2E75B6` | Section headers, focal data series, callout borders, active progress markers |
| **Body Text** | `#2D2D2D` | Bullets and primary labels (high contrast without harsh pure black) |
| **Muted Gray** | `#777777` | Bottom citations, figure source notes, subtle frame numbers |
| **Divider Rule** | `#CCCCCC` | Thin horizontal rules when needed (prefer whitespace) |
| **Callout Highlight** | `#FFF2CC` | Sparse on-chart annotation badges or focal cells |

### Cross-Format Typography Scale

Maintain **one clean sans-serif typeface** throughout (e.g., Arial / Calibri / Helvetica in Office/Web; Fira Sans / Carlito / Helvetica / sans-serif `lmodern` in Beamer/Typst). Use size and weight—never mixed font families—for hierarchy.

| Element | PPTX / Google Slides (`10" × 5.625"`) | LaTeX Beamer (`16:9` `160mm × 90mm`*) | Quarto / Reveal.js (`1600 × 900`) | Typst (`presentation-16-9`) |
| :--- | :--- | :--- | :--- | :--- |
| **Action Title** | `24–28 pt` Bold | `\Large` (`~14.4–17.3 pt` in 11pt/12pt base) | `38–44 px` Bold | `24–26 pt` Bold |
| **Subheader** | `20–22 pt` Bold | `\normalsize`–`\large` Bold | `28–32 px` Bold | `20 pt` Bold |
| **Body Bullets (Floor)** | **`≥ 20 pt`** Regular | `\normalsize` (`11pt`–`12pt` class option) | **`≥ 26 px`** Regular | **`≥ 18–20 pt`** Regular |
| **Chart Labels / Callouts** | `16–18 pt` | `\small` (`~10 pt` native / ≥ 16 pt in exported figs) | `20–24 px` | `15–17 pt` |
| **Bottom Citations** | `12–14 pt` Muted | `\footnotesize` or `\scriptsize` Muted | `16–18 px` Muted | `12–13 pt` Muted |

*\*Note on Beamer scaling: Beamer renders onto a physical `160 mm × 90 mm` (`6.3" × 3.54"`) virtual page that scales ~1.6×–2× to fill a 16:9 projector. Consequently, `\documentclass[11pt,aspectratio=169]{beamer}` or `12pt` projects visually at `~20–24 pt`. Never use `\tiny` for readable body text or tables.*

### Design Anti-Patterns to Avoid

- Decorative icons in colored circles, stock photos, or clip art.
- Full-bleed background images on content slides.
- Heavy Beamer themes with multi-line top navigation trees and colored sidebars (`Warsaw`, `Berkeley`, `Antibes`) that steal 20–30% of slide area.
- Thick accent bars or drop shadows under every slide title.
- Per-bullet animations that force excessive clicking (`\pause` on every line). Use progressive reveal only when building a complex diagram or multi-step derivation.

---

## 4. Format-Specific Implementation Patterns

### 4.1 LaTeX Beamer (`16:9` Clean Academic Standard)

Declutter Beamer completely: disable navigation symbols, enforce white content frames, use `booktabs` for tables, `appendixnumberbeamer` so appendix frames do not inflate the total count, and TikZ overlays for direct figure annotations.

```latex
\documentclass[11pt,aspectratio=169]{beamer}
% Add 'handout' option when exporting printable PDFs without overlays:
% \documentclass[11pt,aspectratio=169,handout]{beamer}

\usepackage[utf8]{inputenc}
\usepackage[T1]{fontenc}
\usepackage{lmodern}
\usepackage{helvet}
\renewcommand{\familydefault}{\sfdefault}

\usepackage{amsmath,amssymb}
\usepackage{booktabs}
\usepackage{graphicx}
\usepackage{tikz}
\usepackage{appendixnumberbeamer}

% --- Color Tokens ---
\definecolor{NavyPrimary}{HTML}{1F4E79}
\definecolor{BlueAccent}{HTML}{2E75B6}
\definecolor{BodyDark}{HTML}{2D2D2D}
\definecolor{MutedGray}{HTML}{777777}
\definecolor{HighlightBg}{HTML}{FFF2CC}
\definecolor{CalloutBg}{HTML}{EBF3FA}

% --- Clean Communication-First Theme ---
\usetheme{default}
\usecolortheme{default}
\setbeamertemplate{navigation symbols}{}
\setbeamercolor{background canvas}{bg=white}
\setbeamercolor{normal text}{fg=BodyDark}
\setbeamercolor{frametitle}{fg=NavyPrimary}
\setbeamercolor{itemize item}{fg=BlueAccent}
\setbeamerfont{frametitle}{size=\Large,series=\bfseries}
\setbeamerfont{framesubtitle}{size=\small}

% Subtle bottom footline: muted frame counter only
\setbeamertemplate{footline}{%
  \hfill%
  \usebeamercolor[fg]{page number in head/foot}%
  \textcolor{MutedGray}{\scriptsize\insertframenumber\,/\,\inserttotalframenumber}%
  \hspace*{1.2em}\vspace*{0.6em}%
}

% Helper macro for bottom-of-slide citations
\newcommand{\slidecite}[1]{%
  \vfill\noindent\textcolor{MutedGray}{\footnotesize #1}%
}

\title{Early Childhood Interventions Yield Persistent\\Earnings Gains Through Age 35}
\author{Jane Smith\textsuperscript{1} \and John Doe\textsuperscript{2}}
\institute{\textsuperscript{1}University of X \quad \textsuperscript{2}Laboratory for Y}
\date{Annual Conference · May 2026}
```

#### Beamer Results Slide Pattern (Figure Left + Annotated, Bullets Right)

```latex
\begin{frame}{Treated children earn 18\% more at age 35 --- largest gains occur in Q1}
  \begin{columns}[T,onlytextwidth]
    \begin{column}{0.58\textwidth}
      \begin{tikzpicture}
        \node[anchor=south west,inner sep=0] (fig) at (0,0) {%
          \includegraphics[width=\linewidth]{figures/fig1_earnings_by_quartile.pdf}%
        };
        \begin{scope}[x={(fig.south east)},y={(fig.north west)}]
          \node[fill=HighlightBg,draw=BlueAccent,rounded corners=2pt,
                font=\footnotesize\bfseries,text=BodyDark,inner sep=4pt]
            at (0.26,0.82) {$\uparrow$\,28\% in Q1};
        \end{scope}
      \end{tikzpicture}
    \end{column}
    \begin{column}{0.38\textwidth}
      \textbf{\textcolor{BlueAccent}{Key Takeaways}}
      \vspace{0.4em}
      \begin{itemize}
        \setlength\itemsep{0.6em}
        \item \textbf{Pooled gain:} +18\% earnings ($p < 0.001$)
        \item \textbf{Heterogeneity:} Q1 effect is $3.5\times$ Q4 (28\% vs.\ 8\%)
        \item \textbf{Precision:} 95\% CI $= [14\%, 22\%]$
      \end{itemize}
    \end{column}
  \end{columns}
  \slidecite{Source: Social Security Administration records (2024); Smith \& Doe (2025).}
\end{frame}
```

#### Beamer Sandwich Title, Conclusions, References & Appendix

```latex
% Dark navy Title Slide (sandwich opening)
{\setbeamercolor{background canvas}{bg=NavyPrimary}
 \setbeamercolor{normal text}{fg=white}
 \setbeamercolor{title}{fg=white}
 \setbeamercolor{author}{fg=CalloutBg}
 \setbeamercolor{institute}{fg=CalloutBg}
 \setbeamercolor{date}{fg=CalloutBg}
 \setbeamertemplate{footline}{}
\begin{frame}[plain]
  \titlepage
\end{frame}}

% Conclusions Slide (final main slide — stays up during Q&A)
{\setbeamercolor{background canvas}{bg=NavyPrimary}
 \setbeamercolor{normal text}{fg=white}
 \setbeamercolor{frametitle}{fg=CalloutBg}
\begin{frame}{Conclusions}
  \large
  \begin{enumerate}
    \setlength\itemsep{0.9em}
    \item \textbf{Persistent impact:} Earnings premium remains significant 30 years post-treatment.
    \item \textbf{Equity-enhancing:} Bottom-quartile gains ($+28\%$) are $3.5\times$ top-quartile gains.
    \item \textbf{Cognitive mechanism:} Age-8 skill gains mediate the full long-run effect.
  \end{enumerate}
  \vfill
  \textcolor{CalloutBg}{\small\texttt{jane.smith@university.edu} \quad|\quad Preprint: \texttt{doi.org/10.xxxx/xxxxx}}
\end{frame}}

% References Slide (white background)
\begin{frame}{References}
  \footnotesize
  \begin{itemize}
    \setlength\itemsep{0.5em}
    \item Cunha, F. \& Heckman, J.\,J. (2007). The Technology of Skill Formation. \textit{AER}, 97(2), 31--47.
    \item Naegle, K.\,M. (2021). Ten simple rules for effective presentation slides. \textit{PLOS Comput.\ Biol.}, 17(12).
  \end{itemize}
\end{frame}

% Appendix (uncounted in main slide total via appendixnumberbeamer)
\appendix
\begin{frame}{Appendix A: Effect remains stable across RD bandwidth choices}
  % Robustness table using booktabs (no vertical lines)
\end{frame}
```

**Beamer Build & Overflow Check:**
```bash
pdflatex -interaction=nonstopmode -halt-on-error slides.tex
pdflatex -interaction=nonstopmode -halt-on-error slides.tex
grep -E "Overfull|Warning" slides.log || true
pdftoppm -png -r 150 slides.pdf qa_pages/slide
```

---

### 4.2 Quarto (`.qmd` — Multi-Target: `revealjs`, `beamer`, `pptx`)

Quarto enables a single reproducible source containing executable Python/R figure chunks and clean Markdown slides.

```yaml
---
title: "Early Childhood Interventions Yield Persistent Earnings Gains Through Age 35"
author: "Jane Smith¹ · John Doe²"
institute: "¹University of X · ²Laboratory for Y"
date: "May 2026"
format:
  revealjs:
    width: 1600
    height: 900
    theme: simple
    slide-number: c/t
    transition: none
    embed-resources: true
    css: academic_slides.css
  beamer:
    aspectratio: 169
    theme: default
    navigation: empty
    fontsize: 11pt
---
```

**Quarto Results Slide Pattern (with `academic_slides.css`):**

```markdown
## Treated children earn 18% more at age 35 — largest gains occur in Q1

:::: {.columns}
::: {.column width="58%"}
![Treatment effects across income quartiles with +28% Q1 highlight.](figures/fig1_earnings.png){width="100%"}
:::

::: {.column width="42%"}
**Key Takeaways**

- **Pooled gain:** +18% earnings ($p < 0.001$)
- **Heterogeneity:** Q1 is $3.5\times$ Q4 (28% vs. 8%)
- **Precision:** 95% CI $= [14\%, 22\%]$
:::
::::

::: aside
Source: Social Security Administration records (2024); Smith & Doe (2025).
:::

## Conclusions {background-color="#1F4E79"}

1. **Persistent impact:** Earnings premium remains significant 30 years post-treatment.
2. **Equity-enhancing:** Bottom-quartile gains (+28%) are $3.5\times$ top-quartile gains.
3. **Cognitive mechanism:** Age-8 skill gains mediate the full long-run effect.

`jane.smith@university.edu` | Preprint: `doi.org/10.xxxx/xxxxx`

## References

- Cunha, F. & Heckman, J. J. (2007). *American Economic Review*, 97(2), 31–47.

## Appendix A: Effect remains stable across RD bandwidth choices {.appendix visibility="uncounted"}

...
```

Minimal `academic_slides.css` for Reveal.js:
```css
.reveal { font-family: Arial, Helvetica, sans-serif; font-size: 28px; color: #2D2D2D; }
.reveal h2 { color: #1F4E79; font-size: 1.45em; font-weight: 700; margin-bottom: 0.6em; }
.reveal aside { font-size: 0.6em; color: #777777; }
```

---

### 4.3 PowerPoint (`.pptx` via `python-pptx` or `PptxGenJS`)

Use `10" × 5.625"` (`16:9` widescreen), `0.5"` minimum margin, `Arial`/`Calibri`, and explicit font floors (`26 pt` title, `20 pt` body, `13 pt` citation).

#### `python-pptx` Pattern (Python / Mamba Environments)

```python
from pptx import Presentation
from pptx.dml.color import RGBColor
from pptx.enum.shapes import MSO_SHAPE
from pptx.util import Inches, Pt

COLORS = {
    "bg": RGBColor(0xFF, 0xFF, 0xFF),
    "primary": RGBColor(0x1F, 0x4E, 0x79),
    "accent": RGBColor(0x2E, 0x75, 0xB6),
    "body": RGBColor(0x2D, 0x2D, 0x2D),
    "muted": RGBColor(0x77, 0x77, 0x77),
    "highlight": RGBColor(0xFF, 0xF2, 0xCC),
}
FONT_FACE = "Arial"
MARGIN = Inches(0.5)

prs = Presentation()
prs.slide_width = Inches(10.0)
prs.slide_height = Inches(5.625)
blank_layout = prs.slide_layouts[6]

slide = prs.slides.add_slide(blank_layout)

# Action title
title_box = slide.shapes.add_textbox(MARGIN, Inches(0.25), Inches(9.0), Inches(0.85))
tf = title_box.text_frame
tf.word_wrap = True
p = tf.paragraphs[0]
p.text = "Treated children earn 18% more at age 35 — largest gains occur in Q1"
p.font.name, p.font.size, p.font.bold = FONT_FACE, Pt(25), True
p.font.color.rgb = COLORS["primary"]

# Left figure + direct annotation badge
slide.shapes.add_picture("figures/fig1_earnings.png", MARGIN, Inches(1.2), width=Inches(5.3))
badge = slide.shapes.add_shape(MSO_SHAPE.ROUNDED_RECTANGLE, Inches(0.75), Inches(1.35), Inches(1.7), Inches(0.45))
badge.fill.solid()
badge.fill.fore_color.rgb = COLORS["highlight"]
badge.line.color.rgb = COLORS["accent"]
btf = badge.text_frame
btf.paragraphs[0].text = "↑ 28% in Q1"
btf.paragraphs[0].font.name, btf.paragraphs[0].font.size, btf.paragraphs[0].font.bold = FONT_FACE, Pt(14), True
btf.paragraphs[0].font.color.rgb = COLORS["body"]

# Right interpretive bullets (>= 20 pt)
notes_box = slide.shapes.add_textbox(Inches(6.1), Inches(1.25), Inches(3.4), Inches(3.5))
ntf = notes_box.text_frame
ntf.word_wrap = True
for idx, text in enumerate([
    "Pooled gain: +18% earnings (p < 0.001)",
    "Heterogeneity: Q1 is 3.5× Q4 (28% vs. 8%)",
    "Precision: 95% CI = [14%, 22%]",
]):
    para = ntf.paragraphs[0] if idx == 0 else ntf.add_paragraph()
    para.text = f"• {text}"
    para.font.name, para.font.size = FONT_FACE, Pt(20)
    para.font.color.rgb = COLORS["body"]
    para.space_after = Pt(12)

# Bottom citation
cite_box = slide.shapes.add_textbox(MARGIN, Inches(5.1), Inches(9.0), Inches(0.35))
cp = cite_box.text_frame.paragraphs[0]
cp.text = "Source: Social Security Administration records (2024); Smith & Doe (2025)."
cp.font.name, cp.font.size = FONT_FACE, Pt(13)
cp.font.color.rgb = COLORS["muted"]
```

For `PptxGenJS` (Node.js) workflows, define the exact same hex constants (`#1F4E79`, `#2E75B6`, `#2D2D2D`, `#777777`), `LAYOUT_16x9`, `0.5"` margins, and `20 pt` minimum body text.

**PPTX Conversion & Visual QA:**
```bash
soffice --headless --convert-to pdf slides.pptx --outdir qa_pages/
pdftoppm -png -r 150 qa_pages/slides.pdf qa_pages/slide
```

---

### 4.4 Typst (`presentation-16-9`)

Typst compiles in milliseconds with native math and crisp grids. Use `#set page(paper: "presentation-16-9")` (or packages such as `touying` / `polylux` for advanced themes and step overlays).

```typst
#set page(paper: "presentation-16-9", margin: (x: 1.4cm, y: 1.1cm), fill: white)
#set text(font: "Arial", size: 19pt, fill: rgb("#2D2D2D"))

#let navy = rgb("#1F4E79")
#let accent = rgb("#2E75B6")
#let muted = rgb("#777777")

#let action_title(body) = {
  text(size: 25pt, weight: "bold", fill: navy, body)
  v(0.5em)
}

#let slide_cite(body) = {
  place(bottom + left, text(size: 12pt, fill: muted, body))
}

// --- Results Slide ---
#action_title[Treated children earn 18% more at age 35 — largest gains occur in Q1]

#grid(
  columns: (1.45fr, 1fr),
  gutter: 1.4em,
  image("figures/fig1_earnings.svg", width: 100%),
  [
    #text(weight: "bold", fill: accent)[Key Takeaways]
    - *Pooled gain:* +18% earnings ($p < 0.001$)
    - *Heterogeneity:* Q1 is $3.5 times$ Q4 (28% vs. 8%)
    - *Precision:* 95% CI $= [14\%, 22\%]$
  ]
)
#slide_cite[Source: Social Security Administration records (2024); Smith & Doe (2025).]
#pagebreak()
```

---

### 4.5 Marp / Markdown Web Slides

For Markdown-native browser/PDF decks:

```markdown
---
marp: true
size: 16:9
paginate: true
style: |
  section { font-family: Arial, Helvetica, sans-serif; background: #FFFFFF; color: #2D2D2D; font-size: 26px; padding: 48px 56px; }
  h1, h2 { color: #1F4E79; font-size: 1.35em; margin-bottom: 0.5em; }
  section.lead { background: #1F4E79; color: #FFFFFF; }
  section.lead h1, section.lead h2 { color: #FFFFFF; }
  .cols { display: grid; grid-template-columns: 1.4fr 1fr; gap: 32px; align-items: start; }
  .cite { position: absolute; bottom: 24px; font-size: 0.55em; color: #777777; }
---

<!-- _class: lead -->
# Early Childhood Interventions Yield Persistent Earnings Gains Through Age 35
Jane Smith¹ · John Doe² | Annual Conference · May 2026

---

## Treated children earn 18% more at age 35 — largest gains occur in Q1

<div class="cols">
  <img src="figures/fig1_earnings.png" alt="Bar chart of treatment effects by income quartile" />
  <div>
    <ul>
      <li><strong>Pooled gain:</strong> +18% earnings (p &lt; 0.001)</li>
      <li><strong>Heterogeneity:</strong> Q1 is 3.5× Q4 (28% vs. 8%)</li>
      <li><strong>Precision:</strong> 95% CI = [14%, 22%]</li>
    </ul>
  </div>
</div>
<div class="cite">Source: Social Security Administration records (2024); Smith &amp; Doe (2025).</div>
```

---

## 5. Verification, Visual QA & Pre-Delivery Checklist

Never deliver a presentation without inspecting rendered output:

1. **Render every slide to PNG** and inspect for text clipping, overlapping labels, tiny chart axes, or crowded margins:
   - **Beamer / Typst / PDF:** `pdftoppm -png -r 150 slides.pdf qa_pages/slide`
   - **PPTX:** `soffice --headless --convert-to pdf slides.pptx && pdftoppm -png -r 150 slides.pdf qa_pages/slide`
   - **Quarto / Marp:** render to PDF or HTML (`quarto render` / `marp --pdf`) and rasterize with `pdftoppm` or inspect in headless browser.
2. **Audit log warnings & word counts:**
   - In LaTeX/Beamer, check `.log` for `Overfull \vbox` (vertical overflow off slide bottom) and `Overfull \hbox` (horizontal clipping).
   - In PPTX, extract slide text (`markitdown` or `python-pptx`) to verify ≤ ~40 body words per slide and ≥ 20 pt body font.

### Academic Presentation QA Checklist

- [ ] **Title slide:** Title framed as a statement or question; full authors, affiliations, venue, and date included.
- [ ] **Research question:** Stated explicitly on its own dedicated slide (Slide 2 or 3).
- [ ] **Action titles:** Every content slide title is a complete takeaway sentence (1–2 lines), not a topic label.
- [ ] **Ghost deck test:** Reading only the slide titles in sequence communicates the full argument.
- [ ] **Exhibit discipline:** Max 1 exhibit per results slide; key finding annotated directly on the figure/table; paper figures rebuilt at presentation font sizes.
- [ ] **Text discipline:** ≤ ~40 body words per content slide; body text readable from back of room (≥ 20 pt PPTX / ≥ 11 pt Beamer base / ≥ 26 px web).
- [ ] **Citations:** Every borrowed figure, dataset, or claim has an in-slide source citation and a full entry on the References slide.
- [ ] **Ending:** **Conclusions** is the final main slide (stays on screen during Q&A; no "Thank You" or blank ending), with contact info / link / QR code.
- [ ] **Appendix:** Clearly labeled and uncounted in main slide numbering, covering anticipated Q&A objections and robustness checks.
- [ ] **Visual & timing check:** All rendered slide PNGs verified clean (zero overlaps/clipping); talk rehearsed 1–2 minutes under time limit.

---

## 6. Sources and Attribution

1. **Gabberflast (2026).** *academic-pptx-skill* (MIT License). Primary upstream source for communication-first academic slide standards, ghost deck testing, action title taxonomy, exhibit discipline, and Q&A conclusions framing: [github.com/Gabberflast/academic-pptx-skill](https://github.com/Gabberflast/academic-pptx-skill). Adapted and extended here to support LaTeX Beamer, Quarto, PowerPoint (`python-pptx`/`PptxGenJS`), Typst, and Marp/Reveal.js.
2. **Minto, B. (2009).** *The Pyramid Principle: Logic in Writing and Thinking*. Financial Times / Prentice Hall (SCR narrative spine, top-down synthesis, action titles).
3. **Naegle, K. M. (2021).** "Ten simple rules for effective presentation slides." *PLOS Computational Biology*, 17(12): e1009554.
4. **Companion Repository Skills:** [SKILLS_scientific_latex_report.md](./SKILLS_scientific_latex_report.md) (LaTeX `booktabs`, math precision, two-pass non-interactive builds) and [SKILLS_clear_academic_english.md](./SKILLS_clear_academic_english.md) (scholarly clarity and precision).

### Upstream MIT License Notice

Per the MIT License of [Gabberflast/academic-pptx-skill](https://github.com/Gabberflast/academic-pptx-skill) (see also [THIRD_PARTY_LICENSES.md](./THIRD_PARTY_LICENSES.md)):

```text
MIT License

Copyright (c) 2026 Gabberflast

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
