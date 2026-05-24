# Technical Breakdown — GEDC Research Analysis Prompt

## Overview

This document describes the architecture of the GEDC Research Analysis Prompt. 
It identifies each logical component, names the prompt engineering construct it represents, and estimates its token footprint.

---

## Prompt Architecture

Two versions of the prompt are documented here.

**gedc_research_analysis.txt** — the original version. 
All organization-specific values (team name, geographies, sectors, funder, date range, connectivity segments) are hardcoded directly into the prompt body. 
The prompt is composed of seven sections.

**gedc_research_analysis_parameterized.txt** — the generalized version. 
A configuration block sits above the same seven sections. 
All organization-specific values are defined once in the config block and referenced throughout the prompt body using `{{VARIABLE_NAME}}` syntax. 
The logic in the seven sections is identical to the original.

The section descriptions below apply to both versions. The configuration block section applies to the parameterized version only.

---

### Configuration Block
*(Parameterized version only)*

**Construct:** Parameterization / variable substitution

The configuration block sits at the top of the prompt and defines all organization-specific values as named variables. 
These variables are referenced throughout the prompt body using `{{VARIABLE_NAME}}` syntax. 
A user adapting the prompt to a different context changes only the config block — the logic in Sections 1–7 remains unchanged.

Parameters defined in the config block:

| Parameter | Purpose |
|---|---|
| `TEAM_NAME` | Full name of the team, used in persona and document header |
| `TEAM_ABBREVIATION` | Short form used throughout the prompt body |
| `DOCUMENT_RECORD_NAME` | Label for the output document header |
| `PUBLICATION_DATE_START` | Earliest qualifying publication year |
| `PUBLICATION_DATE_END` | Latest qualifying publication year |
| `PUBLICATION_DATE_FLAG_BEFORE` | Year before which papers are flagged as older |
| `PRIORITY_GEOGRAPHIES` | List of priority countries or regions for the domain criterion and output field |
| `PRIORITY_SECTORS` | List of priority sectors for the domain criterion |
| `FUNDER_TO_FLAG` | Organization name used in the Funder Match output field |
| `CONNECTIVITY_SEGMENTS` | Definitions of the four connectivity tiers used in screening and analysis |
| `RIGOR_INDEXES` | Named indexes used to verify Tier 1 rigor assignments |

Parameterization separates configuration from logic. It reduces the risk of partial or inconsistent edits when adapting the prompt, and makes the organization-specific assumptions visible and auditable in one place rather than distributed across the prompt.

**Approximate tokens:** 320

---

### Section 1 — Persona

**Construct:** Role prompting / persona injection

The model is assigned a specific professional identity with a defined scope of expertise, audience, and analytical lens. 
This shapes the register, vocabulary, and judgment style of all outputs without requiring explicit instructions in every section.

Persona prompts work by activating relevant knowledge clusters and behavioral patterns. 
The more specific the persona — including institutional context, stakeholder audiences, and named analytical frameworks — the more consistent the output register. 
This persona includes all three layers: expertise domain, institutional context, and output audience.

**Approximate tokens:** 340

---

### Section 2 — Selection Criteria (Qualification Gate)

**Construct:** Conditional branching / hard stop logic

This section implements a binary filter before any analysis is performed. It uses a compound boolean condition: three threshold criteria (all must pass) combined with four domain criteria (at least one must pass). If the condition fails, the model is instructed to halt and produce a structured rejection rather than a partial analysis.

Hard stop logic is a reliability mechanism. Without it, models tend to proceed with analysis even on out-of-scope inputs, producing output that looks valid but is not grounded in the task definition. The explicit halt instruction prevents that behavior.

**Approximate tokens:** 460

---

### Section 3 — Analysis Framework

**Construct:** Structured extraction protocol / dimension-based decomposition

Nine named dimensions define exactly what to extract from the paper and in what order. Each dimension specifies the extraction target, the classification vocabulary (where applicable), and the fallback behavior when the paper does not address the dimension.

Dimension-based decomposition reduces omission errors. Rather than asking for a general summary, the prompt forces coverage of each analytical area independently. The instruction to state 'Not addressed' rather than infer is a scope control mechanism — it prevents the model from filling gaps with plausible-sounding but ungrounded content.

**Approximate tokens:** 910

---

### Section 4 — Methodological Rigor Scoring Guide

**Construct:** Rubric-based classification / multi-axis scoring

This section defines a two-axis scoring system: Tier (publication and institutional source) and Confidence (internal methodological quality). Each axis has discrete levels with verifiable criteria. The two axes are explicitly decoupled — a paper can score high on one and low on the other.

Rubric-based classification reduces model discretion in evaluation tasks. By specifying verifiable criteria (named indexes, named methods, named thresholds), the prompt reduces the model's reliance on general impressions of quality, which are less consistent and harder to audit.

**Approximate tokens:** 960

---

### Section 5 — Output Format

**Construct:** Structured output specification / field-level schema definition

This section defines the exact structure of the output document: layout, section order, field names, formatting rules, default values, and evidence strength rating definitions. It functions as a schema — the output must conform to it regardless of the input paper's content or length.

Output format specifications are most effective when they define both the structure (what fields exist and in what order) and the semantics (what each field should contain and what to do when the content is absent). This section does both.

**Approximate tokens:** 860

---

### Section 6 — Constraints

**Construct:** Negative instruction set / priority override / behavioral guardrails

Seven named constraints govern behavior across all sections. Unlike the analysis framework or output format, constraints are prohibitive rather than directive — they define what the model must not do. They are assigned the highest precedence in the priority hierarchy defined at the top of the prompt.

Placing constraints in a dedicated section with explicit precedence is more reliable than embedding them within individual instructions. Models are more likely to apply a constraint consistently when it is named, separated, and given a stated priority over other instructions.

**Approximate tokens:** 500

---

### Section 7 — Self-Checking Protocol

**Construct:** Chain-of-thought verification / meta-cognitive audit loop

Nine named checks require the model to review its own output before producing the final document. Each check targets a specific failure mode: coverage gaps, ranking errors, rigor misclassification, scope creep, default value errors, cross-paper inconsistency, sycophancy, and hallucination.

Self-checking protocols work by making implicit evaluation criteria explicit and applying them as a mandatory post-processing step. Check 8 (anti-sycophancy) and Check 9 (hallucination audit) are particularly high-value for tasks involving research evaluation, where the model's tendency to confirm plausible-sounding claims is a known failure mode.

**Approximate tokens:** 780

---

## Token Usage by Section

### Original version (gedc_research_analysis.txt)

| Section | Description | Approx. tokens |
|---|---|---|
| Priority hierarchy | Opening instruction block | 170 |
| Section 1 | Persona | 340 |
| Section 2 | Qualification gate | 460 |
| Section 3 | Analysis framework | 910 |
| Section 4 | Rigor scoring guide | 960 |
| Section 5 | Output format + field definitions | 860 |
| Section 6 | Constraints | 500 |
| Section 7 | Self-checking protocol | 780 |
| **Total** | | **~4,980** |

### Parameterized version (gedc_research_analysis_parameterized.txt)

| Section | Description | Approx. tokens |
|---|---|---|
| Configuration block | Parameterized variables | 320 |
| Priority hierarchy | Opening instruction block | 170 |
| Section 1 | Persona | 340 |
| Section 2 | Qualification gate | 460 |
| Section 3 | Analysis framework | 910 |
| Section 4 | Rigor scoring guide | 960 |
| Section 5 | Output format + field definitions | 860 |
| Section 6 | Constraints | 500 |
| Section 7 | Self-checking protocol | 780 |
| **Total** | | **~5,300** |

Note: Token counts are estimated using character and word-count approximations (1 token ≈ 4 characters ≈ 0.75 words). Actual counts will vary slightly by tokenizer and model version. The parameterized version adds approximately 320 tokens over the original due to the config block.

---

## Cost Per Run

*Pricing as of May 2026. Token estimates based on the parameterized version (~5,300 prompt tokens). The original version would cost approximately $0.02 less per run.*

**Model:** Claude Opus 4.7 — the recommended model for quality and reliability.
**Pricing:** $5.00 per million input tokens · $25.00 per million output tokens.

### Token breakdown per run (25-page paper)

| Component | Type | Approx. tokens |
|---|---|---|
| Prompt (all 7 sections + config) | Input | 5,300 |
| Research paper (25 pages, ~500 words/page) | Input | 16,700 |
| Output document (structured table, all fields) | Output | 3,500 |
| Self-check log (9 checks) | Output | 4,000 |
| **Total input** | | **~22,000** |
| **Total output** | | **~7,500** |

### Cost per run

| | Tokens | Rate | Cost |
|---|---|---|---|
| Input | 22,000 | $5.00 / 1M | $0.110 |
| Output | 7,500 | $25.00 / 1M | $0.188 |
| **Total per paper** | | | **~$0.30** |

### Cost at volume

| Papers | Standard | Batch API (50% off) |
|---|---|---|
| 10 | $3.00 | $1.50 |
| 50 | $15.00 | $7.50 |
| 100 | $30.00 | $15.00 |
| 500 | $150.00 | $75.00 |

The Batch API processes requests asynchronously (within 24 hours) at a 50% discount on all token costs. For non-urgent screening workflows this is the most effective cost lever available.

### Notes

- Research paper length is the primary cost driver.
- The self-check log (Section 7) accounts for approximately 53% of output tokens. If cost becomes a consideration at scale, a condensed log mode would be the highest-impact optimization.
- Output tokens cost 5x more per token than input tokens. Verbose field values in the output document have a greater cost impact than prompt length.
- Prompt caching can reduce input costs for the prompt portion (~5,300 tokens) by 90% on repeated runs, saving approximately $0.02 per run. At high volume this compounds but is not the primary cost lever given the paper dominates input tokens.

---

## Construct Summary

| Prompt engineering construct | Where used |
|---|---|
| Parameterization / variable substitution | Configuration block |
| Role prompting / persona injection | Section 1 |
| Conditional branching / hard stop | Section 2 |
| Structured extraction protocol | Section 3 |
| Rubric-based classification | Section 4 |
| Output schema definition | Section 5 |
| Negative instruction / priority override | Section 6 |
| Chain-of-thought verification loop | Section 7 |
| Priority hierarchy (meta-instruction) | Opening block |
