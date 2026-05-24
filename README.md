# GEDC Research Analysis Prompt

A structured prompt that analyzes a single AI or digital inclusion research paper and produces a Word document with a standardized analysis record. Built and used in the context of a development foundation team (Gender Equality & Digital Connectivity) and adapted here for public reference.

## What it does

Given one research paper, the prompt:

1. Applies a qualification gate to confirm the paper is in scope. Out-of-scope papers are rejected with a stated reason and no further analysis is run.
2. Extracts study basics, gender focus, connectivity segmentation, beneficiaries, and inclusion/exclusion criteria.
3. Synthesizes up to three key findings with evidence strength ratings.
4. Assigns a methodological rigor tier (peer-reviewed, gray literature, industry-funded, learning partner) and a separate confidence rating.
5. Produces a strategic relevance assessment.
6. Runs a self-checking protocol covering coverage gaps, scope creep, hallucination, and sycophancy before producing the output.

## Input and output

Input: one research paper, PDF or text.

Output: one Word document (.docx) structured as a two-column table grouped into labeled sections. A self-check log is appended after the main document.

## Repo contents

```
prompt/
  gedc_research_analysis.txt
  gedc_research_analysis_parameterized.txt
docs/
  prompt_technical_breakdown.md
  prompt_evaluation.md
```

## Two versions of the prompt

`gedc_research_analysis.txt` is the original. Team-specific values (geographies, sectors, funder, connectivity framework) are hardcoded directly into the prompt body.

`gedc_research_analysis_parameterized.txt` is generalized. All team-specific values are defined in a configuration block at the top of the file and referenced throughout the prompt body using `{{VARIABLE_NAME}}` syntax. To adapt the prompt to a different team or scope, edit only the configuration block.

See git tags for version history (current state of `main` is the working version; tags mark stable releases).

## How it was built

The prompt is structured into seven sections with an explicit priority hierarchy, named failure modes in the self-check protocol, and rubric-based scoring for rigor and evidence strength.

The design rationale and the prompt engineering constructs used in each section are documented in [`docs/prompt_technical_breakdown.md`](docs/prompt_technical_breakdown.md).

Strengths, limitations, and known optimization opportunities are documented in [`docs/prompt_evaluation.md`](docs/prompt_evaluation.md).

## License

MIT. See [`LICENSE`](LICENSE).