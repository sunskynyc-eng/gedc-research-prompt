# GEDC Research Analysis Prompt

GEDC = Gender Equality & Digital Connectivity

A structured prompt for analyzing AI and digital inclusion research papers on behalf of a development foundation program team.

## What it does

Takes a research paper as input and produces a structured analysis record covering:

- Whether the paper qualifies for analysis (qualification gate)
- Study basics, gender focus, and connectivity segmentation
- Evidence synthesis across up to three key findings
- Methodological rigor tier and confidence rating
- Strategic relevance assessment
- A self-checking protocol to catch scope creep, hallucination, and sycophancy

## Input

A single research paper (PDF or text). One paper per run.

## Output

A formatted Word document (.docx) structured as a two-column table, organized into labeled sections. Includes a self-check log appended after the main document.

## Prompt versions

v1.0 — Last revised April 2026

Two versions are available:

- `gedc_research_analysis_v1.0.txt` — original version with all values hardcoded
- `gedc_research_analysis_v1.0_parameterized.txt` — generalized version with a configuration block at the top. All organization-specific values (team name, priority geographies, sectors, funder, date range, connectivity segments) are defined once in the config block and referenced throughout the prompt body. Use this version to adapt the prompt to a different team, organization, or thematic scope without modifying the logic.

## Contents

```
prompt/
  gedc_research_analysis_v1.0.txt                Original prompt
  gedc_research_analysis_v1.0_parameterized.txt  Parameterized version with config block
docs/
  prompt_technical_breakdown.md                         Prompt architecture and token usage
  prompt_evaluation.md                                  Strengths, limitations, and optimization notes
```
