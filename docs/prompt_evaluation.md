# Evaluation — GEDC Research Analysis Prompt v1.0

## Strengths

**Explicit priority hierarchy**
The opening block defines a precedence order across all seven sections. This reduces ambiguous behavior when instructions conflict. In long prompts, models may otherwise resolve conflicts by recency or salience rather than intent.

**Hard stop at qualification gate**
Section 2 halts analysis entirely if a paper fails screening. This prevents the model from producing partial or out-of-scope output that looks valid but is not. Without the halt instruction, models tend to proceed and produce something plausible but incorrect.

**Decoupled rigor scoring**
Section 4 separates Tier (publication source) from Confidence (methodological quality). This is methodologically sound. A peer-reviewed paper can have weak methodology, and a gray-literature report can be rigorous. Conflating the two produces unreliable quality assessments.

**Named failure modes in self-checking**
Section 7 targets specific, named failure modes rather than asking for a general review. Check 8 (anti-sycophancy) and Check 9 (hallucination audit) target two significant error types in research evaluation tasks: confirming what should be good versus what the evidence actually supports, and generating plausible-sounding details not present in the source.

**Consistent default value vocabulary**
Section 5 defines three distinct defaults (Not stated, Not addressed, uncertain with reason) and prohibits common substitutes (N/A, Unknown). This produces cleaner, more auditable output and reduces post-processing effort.

**Scope creep controls**
Constraint 4 in Section 6 explicitly prohibits four specific scope creep patterns: population generalization, causal inference from correlation, pilot-to-scale assumption, and treating author recommendations as findings. These are well-documented error types in AI-assisted research synthesis.

---

## Best Use Cases

**High volume paper screening**
The qualification gate and structured output make this prompt well-suited to screening large batches of papers against defined criteria. The hard stop on non-qualifying papers keeps processing time and cost proportionate to paper relevance.

**Multi-analyst consistency**
The rubric-based scoring in Sections 4 and 5 reduces analyst-to-analyst variation. When multiple people run the same prompt on different papers, the classification criteria are explicit enough to produce comparable outputs without calibration sessions.

**Audit-ready research synthesis**
The self-check log is appended to each output document and traces the reasoning behind tier assignments, evidence strength ratings, and scope decisions. This makes individual outputs auditable and supports quality review without requiring access to the original papers.

**Onboarding new team members to a research framework**
The connectivity segmentation framework, tiering system, and field definitions in this prompt encode institutional knowledge that would otherwise require significant training time to transfer. Running new analysts through the prompt output is a faster path to shared vocabulary.

---

## Limitations

**Single paper per run**
The prompt is designed for one paper per session. It references cross-paper consistency in Check 7 but does not support batch comparison or synthesis across multiple papers in a single output. Running comparisons requires a separate aggregation step.

**Output format is fixed**
Section 5 defines a specific .docx format. Adapting the prompt for a different output format (dashboard input, database record, JSON) requires rewriting Section 5 in full. The format is well-specified but not modular.

**Tier 1 verification depends on model knowledge**
The one exception in Constraint 1 allows the model to draw on training knowledge to confirm whether a journal is indexed in Scopus, Web of Science, PubMed, or DOAJ. This works for well-known journals but is unreliable for regional or newer publications. A lookup step against an external index would be more reliable.

**No handling for multi-paper documents**
If the uploaded file contains more than one paper (e.g., a report with multiple studies), the prompt does not specify how to handle this. The model will typically treat the document as a single paper, which may produce incorrect study basics fields.

**Token cost scales with paper length**
The prompt itself is approximately 5,300 tokens. A 25-page paper adds approximately 16,700 tokens. At high volumes, this is a meaningful cost factor, and the self-checking protocol (Section 7) adds significant output tokens per run. See the technical breakdown for a full cost estimate.

**Prompt is optimized for Claude**
The priority hierarchy, constraint structure, and self-checking protocol are designed around Claude's instruction-following behavior. The logic order, particularly the precedence rules in the opening block and the hard stop in Section 2, may not translate directly to other models. Different models handle conflicting instructions and halt conditions in different ways. Using this prompt with a different model without adjusting the instruction order and constraint framing is likely to produce inconsistent results.

---

## Optimization Opportunities

**Modularize by use case**
Sections 1-3 (persona, gate, framework) are stable across use cases. Sections 4-5 (scoring guide and output format) are more context-specific. Splitting the prompt into a stable core and swappable output modules would reduce maintenance overhead when the output format needs to change.

**Add a pre-check for file type and length**
Before the qualification gate, a short instruction to assess whether the uploaded file is readable and whether it appears to be a single paper would catch file processing failures earlier and reduce wasted processing on malformed inputs. This is partially addressed in Constraint 6 but not as a proactive check.

**Parameterize the connectivity segmentation framework** *(implemented in v1.0 parameterized version)*
The four connectivity segments and all other organization-specific values, including team name, priority geographies, sectors, funder, date range, and rigor indexes, are now defined in a configuration block at the top of the prompt. The prompt body references these as `{{VARIABLE_NAME}}` placeholders. A user adapting the prompt changes only the config block.

**Add a version-controlled changelog**
The prompt is at v1.0 with a revision date but no changelog. Tracking what changed between versions and why would support quality review and make it easier to attribute output differences to prompt changes versus model behavior.

**Reduce self-check log verbosity for high-volume runs**
The self-checking protocol is thorough but produces lengthy logs. For high-volume screening, a condensed log format (pass/fail per check with a flag for any corrections made) would reduce output length and cost while preserving auditability. The full protocol could be retained for spot-check runs.
