* * *

## name: pdf-design-system  
description: "Locked PDF design system with specific typography, color palette, and layout rules for generating professional PDFs. Use when the user wants to generate PDF documents (reports, CVs, invoices, certificates, newsletters) with a consistent brand identity. Includes: Inter/Helvetica font stack, Royal Blue #4361EE accent color, teal #2196A4 secondary, near-black #1a1a1a text, no dashes rule, A4 page format. Always use this skill when the user references their 'locked design system', 'design guidelines', 'brand guidelines', or requests any PDF generation following a specific style."

# PDF Design System

## LOCKED — Do Not Change These Values

### Font

*   Primary: `'Inter'` (Google Fonts), fallback Helvetica, Arial, sans-serif
    
*   Load via: `@import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');`
    

### Color Palette

| Token | Hex | Usage |
| --- | --- | --- |
| Royal Blue | `#4361EE` | Section headers, table headers, underlines, key numbers |
| Near Black | `#1a1a1a` | Name, job titles, skill labels |
| Teal Blue | `#2196A4` | Subtitle/tagline, company lines, subsections |
| Dark Gray | `#333333` | Body text, bullets, paragraphs |
| Medium Gray | `#666666` | Contact line, dates, secondary text, footer |

### Font Sizes

| Element | Size | Weight | Color |
| --- | --- | --- | --- |
| Document title | 22pt | 700 | #1a1a1a |
| Name (CV) | 16pt | 700 | #1a1a1a |
| Section header | 12pt | 700, ALL CAPS | #4361EE |
| Subtitle / tagline | 10pt to 12pt | 400 | #2196A4 |
| Subsection header | 11pt | 700 | #2196A4 |
| Job title | 11pt | 700 | #1a1a1a |
| Company line | 10pt | italic | #2196A4 |
| Body / bullet text | 10pt | 400 | #333333 |
| Dates / secondary | 9.5pt | italic | #666666 |
| Footer / small | 8.5pt | italic | #666666 |

### Layout

*   **Page size**: A4
    
*   **Margins**: 1.2cm top/bottom, 1.6cm left/right
    
*   **Section headers**: ALL CAPS with thin underline in #4361EE
    
*   **Line height**: 1.45 to 1.5 for body text
    

### Page Numbers

*   Include page numbers on all pages EXCEPT the title page
    
*   Position: bottom right corner (footer)
    
*   Format: plain number (e.g. "2" not "Page 2")
    
*   Use CSS `@page` with `counter(page)` — WeasyPrint native
    

### PDF Generation Engine

*   Use **WeasyPrint** (`weasyprint.HTML('file.html').write_pdf('file.pdf')`) for all PDF generation
    
*   Do NOT use playwright-cli, reportlab, or pypdf for creating new PDFs from HTML
    
*   WeasyPrint is pre-installed; if missing: `pip install weasyprint --break-system-packages`
    
*   Verify output with: `python3 -c "from pypdf import PdfReader; r = PdfReader('file.pdf'); print(len(r.pages), 'pages')"`
    

### HARD RULES (Never Break)

1.  **No dashes of any kind**: Never use em dash (—), en dash (–), or double hyphen (--). Use commas, colons, parentheses, the word "to" for ranges, or "|" as a separator.
    
2.  **No fabrication**: Do not invent skills, titles, employers, dates, or metrics. Reframe real experience only.
    
3.  **Date ranges**: Use "to" (e.g. "2023 to 2025"), never hyphens.
    
4.  **Separator character**: Use "|" between title and focus area (e.g. "Bioinformatics Research Engineer | Multi-Omics Systems Biology").
    

## Template Structure

### HTML Template Pattern (for WeasyPrint PDF generation)

See references/html-template.html for the base template.

### CSS-in-HTML Pattern

Embed styles directly in `<style>` tags within the HTML file. Always include:

1.  `@import` for Google Fonts (Inter)
    
2.  `@page` rule with A4 size and margins
    
3.  CSS variables or direct hex values for the color palette
    
4.  `-webkit-print-color-adjust: exact` and `print-color-adjust: exact` on body
    

### Title Page

*   No page number on title page (handled by `@page :first` CSS rule)
    
*   Use `page-break-after: always` on title page div
    
*   Use `min-height: 24cm` on title page to fill the A4 page
    
*   Content pages start page numbering from 2 automatically
    

### Tables

*   All columns: left-aligned
    
*   Numeric columns:  `font-variant-numeric: tabular-nums`
    
*   Header row: background #4361EE, white text
    
*   Alternating row backgrounds: even rows get #f8f9ff via `tr:nth-child(even) td`
    

### Section Order (CV)

1.  PROFILE
    
2.  PROFESSIONAL EXPERIENCE (reverse chronological)
    
3.  EDUCATION (reverse chronological)
    
4.  SKILLS (grouped by category)
    
5.  HONORS AND AWARDS
    
6.  LANGUAGES
    

*   Drop empty sections entirely
    

## Workflow

1.  User provides content and references the design system
    
2.  Create an HTML file with embedded CSS following the rules above  
    (use the template at references/html-template.html as the starting point)
    
3.  Convert the HTML to PDF using **WeasyPrint**:
    
    ```python
    from weasyprint import HTML
    HTML('path/to/file.html').write_pdf('path/to/output.pdf')
    ```
    
4.  Verify the output:
    
    ```bash
    python3 -c "
    from pypdf import PdfReader
    import os
    r = PdfReader('path/to/output.pdf')
    size_kb = os.path.getsize('path/to/output.pdf') / 1024
    print(f'Pages: {len(r.pages)}, Size: {size_kb:.0f} KB')
    "
    ```
    
5.  Copy the PDF to the shared outputs directory:
    
    ```bash
    cp path/to/output.pdf /mnt/user-data/outputs/filename.pdf
    ```
    
6.  Provide the download link:  
    `http://localhost:8081/files/default/filename.pdf`
    
7.  (Optional) Also call `file-generator_create_file` with  
    `data={"format":"pdf","filename":"filename.pdf"}` and `persistent=true`  
    for a managed download link as backup.
    

### Google Fonts in WeasyPrint (Why No Timing Race)

WeasyPrint handles Google Fonts differently from a browser:

*   When WeasyPrint parses the CSS and encounters `@import url('https://fonts.googleapis.com/...')`, it makes a **synchronous HTTP request** to Google Fonts during the parsing phase — before any text is rendered
    
*   The font files (WOFF2) are downloaded and **embedded directly into the PDF** during the single conversion pass
    
*   There is **no timing race** — font loading is part of the conversion pipeline, not an async process
    
*   If the Google Fonts CDN is unreachable (no outbound internet), the CSS `font-family` fallback stack (`'Inter', Helvetica, Arial, sans-serif`) ensures text still renders with a professional system font
    
*   You can verify Inter is embedded by checking the PDF file size (Inter adds ~150-200 KB of font data) or by inspecting text rendering in any PDF viewer
    

### Verify Font Rendering

```bash
# Inter-embedded PDFs are typically larger than fallback-only PDFs
python3 -c "
import os
size = os.path.getsize('output.pdf')
if size > 150000:
    print('PDF size suggests fonts are embedded (good sign)')
else:
    print('PDF is small — fonts may have fallen back (check network)')
"
```

## Container Separation: Critical for File Serving

The environment runs **two separate Docker containers**:
| Container | Purpose | Writable Path |
| --- | --- | --- |
| Open Computer Use | Where bash, Python, and file creation execute | `/home/assistant/` |
| File Generator Service | Provides download links to the user | Reads from shared mounts |

### The Critical Rule

*   Files saved at `/home/assistant/...pdf` are **invisible** to the file generator service — do not point users there
    
*   The directory `/mnt/user-data/outputs/` is a **shared mount** visible to both containers
    
*   The URL `http://localhost:8081/files/default/filename.pdf` serves files from this shared directory
    
*   Always copy the final PDF there before providing a download link
    

### Never Do This

```
WRONG: "Your file is at /home/assistant/report.pdf"     [user can't access it]
WRONG: "Click http://localhost:9003/..." (without also copying to outputs) [service routing may fail]
```

### Always Do This

```
RIGHT: cp report.pdf /mnt/user-data/outputs/report.pdf
       Then share: http://localhost:8081/files/default/report.pdf
RIGHT: Also call file-generator_create_file for a managed link as backup
```

## Common Failure Modes and Fixes

| Symptom | Cause | Fix |
| --- | --- | --- |
| Single blank page or only last page | Playwright/ browser-based tool captured viewport instead of full document | Use WeasyPrint — it renders the full document natively |
| "Not Available" on download link | File saved in wrong container (e.g. /home/assistant/) | Copy to `/mnt/user-data/outputs/` before sharing |
| Wrong fonts in PDF | Google Fonts CDN unreachable | Font fallback stack ensures professional output either way |
| PDF has no content / empty pages | Missing `page-break-after: always` on title page or `page-break-before: always` on content | Verify HTML has proper page break CSS rules |
| Table colors not showing | Missing `print-color-adjust: exact` in CSS | Add `-webkit-print-color-adjust: exact; print-color-adjust: exact` to body |
| Wrong page size | Missing or incorrect `@page { size: A4; }` rule | Ensure `@page` rule is present with `size: A4` |

## Quick Reference: All Design Tokens

### Colors

```css
--royal-blue: #4361EE;    /* headers, tables, underlines */
--teal: #2196A4;          /* subsections, subtitles */
--near-black: #1a1a1a;    /* titles */
--dark-gray: #333333;     /* body text */
--medium-gray: #666666;   /* dates, footer */
--light-bg: #f8f9ff;      /* alternating table rows */
```

### Page Setup

```css
@page {
  size: A4;
  margin: 1.2cm 1.6cm;
}
```

### Section Header Pattern

```css
.section-header {
  font-size: 12pt; font-weight: 700; color: #4361EE;
  text-transform: uppercase; letter-spacing: 0.08em;
  border-bottom: 1px solid #4361EE;
}
```

### Title Page Pattern

```css
.title-page {
  display: flex; flex-direction: column; justify-content: center;
  min-height: 24cm; page-break-after: always;
}
```