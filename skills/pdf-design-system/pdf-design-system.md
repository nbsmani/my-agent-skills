---
name: pdf-design-system
version: 1.0.0
type: design-system
description: "Locked PDF design system with specific typography, color palette, and layout rules"
formats: [pdf, docx, html, qmd, txt]
---

LOCKED — Do Not Change These Values

FONT:
  Primary: 'Inter' (Google Fonts), fallback Helvetica, Arial, sans-serif
  Load via: @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap')

COLOR PALETTE:
  Royal Blue: #4361EE -> Section headers, table headers, underlines, key numbers
  Near Black: #1a1a1a -> Name, job titles, skill labels
  Teal Blue: #2196A4 -> Subtitle/tagline, company lines, subsections
  Dark Gray: #333333 -> Body text, bullets, paragraphs
  Medium Gray: #666666 -> Contact line, dates, secondary text, footer

FONT SIZES:
  Document title: 22pt, 700, #1a1a1a
  Name (CV): 16pt, 700, #1a1a1a
  Section header: 12pt, 700 ALL CAPS, #4361EE
  Subtitle/tagline: 10pt to 12pt, 400, #2196A4
  Subsection header: 11pt, 700, #2196A4
  Job title: 11pt, 700, #1a1a1a
  Company line: 10pt, italic, #2196A4
  Body/bullet text: 10pt, 400, #333333
  Dates/secondary: 9.5pt, italic, #666666
  Footer/small: 8.5pt, italic, #666666

LAYOUT:
  Page size: A4
  Margins: 1.2cm top/bottom, 1.6cm left/right
  Section headers: ALL CAPS with thin underline in #4361EE
  Line height: 1.45 to 1.5 for body text

PAGE NUMBERS:
  Include on all pages EXCEPT the title page
  Position: bottom right corner (footer)
  Format: plain number (e.g. "2")
  Use CSS @page with counter(page)

PDF GENERATION ENGINE:
  Use WeasyPrint: weasyprint.HTML('file.html').write_pdf('file.pdf')
  Do NOT use playwright-cli, reportlab, or pypdf

HARD RULES (Never Break):
  1. No dashes of any kind: Never use em dash, en dash, or double hyphen
  2. No fabrication: Do not invent skills, titles, employers, dates, or metrics
  3. Date ranges: Use "to" (e.g. "2023 to 2025"), never hyphens
  4. Separator character: Use "|" between title and focus area

================================================================================
EMBEDDED HTML TEMPLATE (Copy this block exactly when generating PDFs)
================================================================================

[START HTML TEMPLATE]
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<style>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');

@page {
  size: A4;
  margin: 1.2cm 1.6cm;
  @bottom-right {
    content: counter(page);
    font-family: 'Inter', Helvetica, Arial, sans-serif;
    font-size: 8.5pt;
    color: #666666;
    font-style: italic;
  }
}

@page :first {
  @bottom-right { content: none; }
}

* { margin: 0; padding: 0; box-sizing: border-box; }

body {
  font-family: 'Inter', Helvetica, Arial, sans-serif;
  color: #333333;
  font-size: 10pt;
  line-height: 1.5;
  -webkit-print-color-adjust: exact;
  print-color-adjust: exact;
}

.title-page {
  display: flex;
  flex-direction: column;
  justify-content: center;
  min-height: 24cm;
  page-break-after: always;
}

.doc-title {
  font-size: 22pt;
  font-weight: 700;
  color: #1a1a1a;
  margin-bottom: 0.3cm;
  letter-spacing: 0.02em;
}

.doc-subtitle {
  font-size: 12pt;
  color: #2196A4;
  margin-bottom: 0.5cm;
}

.doc-meta {
  font-size: 9.5pt;
  color: #666666;
  margin-bottom: 1cm;
  padding-bottom: 0.4cm;
  border-bottom: 1px solid #4361EE;
}

.doc-abstract-label {
  font-size: 10pt;
  font-weight: 700;
  color: #4361EE;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  margin-bottom: 0.2cm;
}

.doc-abstract {
  font-size: 10pt;
  color: #333333;
  line-height: 1.5;
}

.section {
  margin-bottom: 0.4cm;
}

.section-header {
  font-size: 12pt;
  font-weight: 700;
  color: #4361EE;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  margin-bottom: 0.15cm;
  padding-bottom: 0.08cm;
  border-bottom: 1px solid #4361EE;
}

.subsection-header {
  font-size: 11pt;
  font-weight: 700;
  color: #2196A4;
  margin-bottom: 0.1cm;
  margin-top: 0.2cm;
}

p {
  font-size: 10pt;
  color: #333333;
  line-height: 1.5;
  margin-bottom: 0.2cm;
}

.table-wrap {
  margin: 0.2cm 0;
  width: 100%;
}

table {
  width: 100%;
  border-collapse: collapse;
  font-size: 9pt;
  font-family: 'Inter', Helvetica, sans-serif;
}

table th {
  background: #4361EE;
  color: white;
  font-weight: 600;
  padding: 0.2cm 0.3cm;
  text-align: left;
  font-size: 9pt;
}

table th.right {
  text-align: right;
}

table td {
  padding: 0.15cm 0.3cm;
  border-bottom: 0.5px solid #ddd;
  color: #333333;
  font-size: 9pt;
}

table td.right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

table tr:nth-child(even) td {
  background: #f8f9ff;
}

.bullets {
  padding-left: 0.3cm;
  margin-bottom: 0.2cm;
}

.bullets .item {
  margin-bottom: 0.1cm;
  position: relative;
  padding-left: 0.6cm;
  text-indent: -0.6cm;
  font-size: 10pt;
  color: #333333;
  line-height: 1.5;
}

.bullets .item::before {
  content: "\2022 ";
  position: relative;
}

.step-list {
  padding-left: 0.3cm;
  margin-bottom: 0.2cm;
}

.step-item {
  margin-bottom: 0.12cm;
  position: relative;
  padding-left: 0.6cm;
  text-indent: -0.6cm;
  font-size: 10pt;
  color: #333333;
  line-height: 1.5;
}

.step-item .num {
  color: #4361EE;
  font-weight: 700;
}

.formula-box {
  background: #f8f9ff;
  border-left: 3px solid #4361EE;
  padding: 0.3cm 0.4cm;
  margin: 0.2cm 0;
  font-size: 10pt;
  color: #1a1a1a;
  line-height: 1.6;
}

.formula-box .hl {
  color: #4361EE;
  font-weight: 600;
}

.highlight-box {
  background: #f0f4ff;
  border: 1px solid #4361EE;
  border-radius: 4px;
  padding: 0.3cm 0.4cm;
  margin: 0.2cm 0;
}

.highlight-box .label {
  font-size: 9pt;
  font-weight: 700;
  color: #4361EE;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  margin-bottom: 0.1cm;
}

.highlight-box .content {
  font-size: 10pt;
  color: #333333;
  line-height: 1.5;
}

.skills-group {
  margin-bottom: 0.12cm;
}

.skills-label {
  font-size: 10pt;
  font-weight: 700;
  color: #1a1a1a;
}

.skills-text {
  font-size: 10pt;
  color: #333333;
}

.lang-row {
  font-size: 10pt;
  color: #333333;
  margin-bottom: 0.08cm;
}

.lang-name { font-weight: 600; }
.lang-level { color: #666666; font-style: italic; }

.name {
  font-size: 16pt;
  font-weight: 700;
  color: #1a1a1a;
  margin-bottom: 0.1cm;
  letter-spacing: 0.02em;
}

.subtitle {
  font-size: 10pt;
  color: #2196A4;
  margin-bottom: 0.15cm;
}

.contact {
  font-size: 9.5pt;
  color: #666666;
  margin-bottom: 0.4cm;
  padding-bottom: 0.3cm;
  border-bottom: 0.5px solid #cccccc;
}

.entry {
  margin-bottom: 0.25cm;
}

.entry-title {
  font-size: 11pt;
  font-weight: 700;
  color: #1a1a1a;
}

.entry-company {
  font-size: 10pt;
  font-style: italic;
  color: #2196A4;
}

.entry-date {
  font-size: 9.5pt;
  font-style: italic;
  color: #666666;
}

.entry-header {
  display: flex;
  justify-content: space-between;
  align-items: baseline;
  margin-bottom: 0.05cm;
}

.entry-sub {
  display: flex;
  justify-content: space-between;
  align-items: baseline;
  margin-bottom: 0.1cm;
}

.page-break {
  page-break-before: always;
}

.footer-note {
  font-size: 8.5pt;
  color: #666666;
  font-style: italic;
  margin-top: 0.5cm;
  padding-top: 0.2cm;
  border-top: 0.5px solid #cccccc;
  text-align: center;
}
</style>
</head>
<body>
<!-- CONTENT GOES HERE -->
</body>
</html>
[END HTML TEMPLATE]

================================================================================
FORMAT-SPECIFIC INSTRUCTIONS
================================================================================

FOR PDF OUTPUT:
  1. Use the HTML template above
  2. Generate with WeasyPrint
  3. Save to /home/assistant/filename.pdf
  4. Copy to /mnt/user-data/outputs/filename.pdf
  5. Serve from http://localhost:8081/files/default/filename.pdf

FOR DOCX OUTPUT:
  1. Convert HTML to DOCX using python-docx or pandoc
  2. OR use the HTML template as a Word document (Word can open HTML)
  3. Save and serve from the same locations

FOR QMD (Quarto Markdown) OUTPUT:
  1. Adapt design tokens to YAML frontmatter
  2. Use color palette in Quarto themes
  3. Example YAML:
     ---
     format: pdf
     font: Inter
     mainfont: Inter
     fontsize: 10pt
     colorlinks: true
     linkcolor: "#4361EE"
     ---

FOR HTML OUTPUT:
  1. Use the same HTML template directly
  2. Add DOCTYPE and serve as standalone
  3. For web use, remove @page rules and adjust for screen

================================================================================
FILE SERVING RULES (CRITICAL)
================================================================================

The EXACT URL format is:
  - http://localhost:8081/files/default/filename.pdf

The path MUST include "/files/default/" between the port and filename.

DO NOT use:
  - http://localhost:8081/filename.pdf (WRONG - missing /files/default/)
  - http://localhost:9003/files/default/filename.pdf (WRONG - wrong port)

ALWAYS USE PORT 8081 FOR:
  - PDF: http://localhost:8081/files/default/filename.pdf
  - DOCX: http://localhost:8081/files/default/filename.docx
  - HTML: http://localhost:8081/files/default/filename.html
  - QMD: http://localhost:8081/files/default/filename.qmd


NEVER USE PORT 9003 FOR:
  - PDF (binary files get corrupted)
  - DOCX (binary Word files)
  - Any binary format

SAFE FOR PORT 9003:
  - TXT
  - MD (Markdown)
  - CSV
  - JSON

WORKFLOW:
  1. Agent reads this skill file (plain .md - readable)
  2. Extracts the embedded HTML template
  3. Applies design rules to content
  4. Generates output in requested format
  5. Saves to /home/assistant/
  6. Copies to /mnt/user-data/outputs/
  7. Serves via http://localhost:8081/files/default/
