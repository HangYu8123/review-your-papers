---
name: review-paper
description: Draft or revise a peer review of any research paper in Hang's reviewing style, with an interactive review page where each point includes evidence and can be accepted, edited, or rejected. Use when asked to review, referee, critique, or assess a manuscript, draft reviewer comments, fill a venue's review form, or revise a review from saved decisions. Follow the review guidelines and the portable writing style guide, and verify any reference review against the manuscript. When asked to edit a manuscript's text instead, propose the edits on the editing page for accept or reject.
---

# Paper review

The output is a draft for Hang to judge, not a finished review. Hang reads each point in the review page, looks at the evidence right under it, and accepts, edits, or rejects it. So two things matter most. Every point must be correct about the paper, because a wrong claim in a review is unfair to the authors and costs Hang credibility. And every point must show its evidence, so Hang can decide in seconds instead of re-reading the paper.

Apply this workflow to the paper being reviewed. Determine its contributions, terminology, evidence, venue, and review identifier from the supplied materials. Do not carry a previous paper's methods, datasets, results, criticisms, or recommendation into a new review.

## Using this skill

The user's request (in Claude Code, the text after the skill's slash command) names the paper folder or manuscript. If it does not, use the paper already established in the conversation, and ask only for missing input.

This skill runs in Claude Code and in Codex, as part of the `review-your-papers` plugin or installed on its own. `SKILL_DIR` below is the folder that holds this `SKILL.md`; resolve relative paths from it. This skill uses three sibling skills, shared with the `advisor-review-paper` skill, that sit next to it in the plugin and in a standalone install: `writing-style-portable` (the portable writing style guide), `writing-style` (the full style references), and `html-templates` (the review and editing templates). Read their files directly from `SKILL_DIR/../<skill name>/`.

| What | Where |
| --- | --- |
| Manuscript | The supplied PDF, editable manuscript, or passage relevant to the request. Use the available supplement and venue form where relevant. |
| Reference review, optional | Usually `<paper>/review.pdf`, or a file the user names. Treat it as a second opinion to verify against the manuscript. |
| Review rules | [references/guideline.md](references/guideline.md), Hang's guideline for the sections, their order, and the rules for each one |
| Past reviews, optional | `previous_reviews/previous_reviews` in the folder that holds the paper folder, or a file Hang names. They are confidential, so they are not part of this skill. |
| Writing style | The [writing-style-portable](../writing-style-portable/SKILL.md) skill. The full references in the `writing-style` skill, `SKILL_DIR/../writing-style/writing_style_ref/` and `SKILL_DIR/../writing-style/committee_style_ref/`, only when needed (see step 5). |
| Review template | `SKILL_DIR/../html-templates/review_template/`. Its `review.html` header comment (lines 1 to 116) is the specification of `review-data.js`. |
| Editing template | `SKILL_DIR/../html-templates/edit_template/`. Used only for edits to a manuscript's text (see "Edits to a manuscript's text"). |
| Checker and format example | `SKILL_DIR/review-tools/check_review.js` and [review-data-format.md](review-tools/review-data-format.md), in this skill |
| Output | `<paper>/review/review.html`, an unchanged copy of the template, and `<paper>/review/review-data.js` |

Below, `<review-template>` and `<edit-template>` stand for these two template folders.

When Hang asks to revise an existing review, update that review in place, preserving saved decisions and Hang's wording. Before replacing an existing review with a fresh draft, ask whether to replace it or write to `<paper>/review-<YYYYMMDD>/` instead.

Treat an unpublished submission, its title, text, data and supplements, and the review itself as confidential. Follow any stricter venue rule, including rules about AI assistance. Keep confidential material out of search engines, web forms, and external tools. When the venue permits a related-work search, use general topic terms and public bibliographic details of published work.

## Ask the authors, do not explain for them

A review of someone else's paper is an external review, and it is the usual case for this skill. Treat a review as external unless Hang says the paper is Hang's own or a co-author's.

In an external review, do not analyze an observation on the authors' behalf. When a point rests on something the paper shows but does not explain, such as an unexpected result, a gap between two conditions, identical numbers in two rows, or a step that does not seem to follow, state the observation with its location and ask the authors to explain or clarify it. A reviewer's guess at the cause can be wrong, it answers the question for the authors, and the authors may read it as a claim they now have to rebut.

- Write "Table 2 shows a lower success rate for the method in cluttered scenes than in open scenes (p. 6). Please explain what causes this drop, since the claim of robustness to clutter depends on it." Do not write "The drop is likely because the sampler cannot find collision-free grasps in clutter."
- Do not follow an observation with "likely because", "possibly due to", "this suggests that", or "one explanation is". If a possible cause helps Hang judge whether the question is worth asking, put it in the evidence `reason`, marked as untested, and keep the review text to the observation and the question.
- Keep the question tied to what depends on it. Name the claim that the answer affects, as a weakness already does, so the authors know why it is asked.
- Checking is still part of the review. Search the paper, recompute numbers, and work through a derivation before asking, so the question is about what the paper really leaves open and not about something it states. This rule is about what the review text says, not about how much you verify.

The rule applies to the weaknesses, the areas for improvement, and the recommendation. The summary and the strengths describe what the paper establishes and need no questions.

When the paper is Hang's own or a co-author's, an explanation of an observation and a suggested fix are useful, since the author can act on them. Mark such an explanation as a reading, not a finding.

## Workflow

### 1. Read the material needed for the review

For a full review, read the available manuscript, appendices, and supplementary material that documents its claims. For extensive supplementary datasets or code, inspect what the material claims depend on. For a scoped review, trace the relevant claim and support, and limit conclusions to what was checked. Search the manuscript and its supplements before reporting an omission.

Use readable source directly. For PDFs, use page-marked extraction such as `python SKILL_DIR/scripts/pdf_pages.py <paper.pdf> <scratchpad>/paper.txt`. Inspect rendered tables, figures, and equations for details the extraction may lose. Verify every reported number at its source or document the calculation from verified inputs.

Keep concise notes on the claims and their locations, what is new, what supports each claim, stated limitations, and unresolved questions that matter to the recommendation. For empirical work, include design, samples, comparisons, measures, and uncertainty as applicable. For other work, record the premises, sources, or synthesis method. Note the venue, submission identifier if given, and any limits on the material reviewed.

### 2. Check the claims against the evidence

For each material claim, identify its support and test the inference under the paper's own methods and scope. In an empirical paper, check design, sampling, comparators, outcome definitions, uncertainty, and whether the relevant setting was tested. In theoretical work, trace the result through its premises and proof. In qualitative, interpretive, or synthesis work, check the evidence, selection method where applicable, and how the conclusions follow. Follow the paper's actual form when it combines these approaches.

Recompute a consequential derived result when the inputs and calculation are available. Check whether differences in data versions, inclusion rules, denominators, scoring, or assumptions could explain a mismatch. State any unresolved check as an observation and a question about the affected claim. Search the relevant manuscript and supplement for alternate terminology before asserting an omission. Read [references/calibration.md](references/calibration.md) before drafting a weakness.

### 3. Look at the closest related work

For a full review, check the closest relevant prior work when the contribution depends on a novelty claim. In a scoped review, do so when the question calls for it. Follow the venue's confidentiality rules and use public search terms. Read the relevant passages of the primary sources and identify the specific difference and version. Record the search scope and any unavailable sources in the evidence. Describe a bounded comparison or ask a focused question when the comparison remains open.

### 4. Check the reference review, if there is one

List each of its points and decide whether it holds, partly holds, or does not hold (a misreading, already addressed in the paper, or minor). Verify by searching `paper.txt` and by checking the passage, derivation, or table it refers to. Check alleged omissions and errors, the venue, generic praise, and requests for additional analyses. Use verified points in your own words, and record each decision in the `reason` of the related evidence, such as "The reference review's missing-control concern is supported by the study design." List the dropped points, with why, in the evidence of the recommendation.

### 5. Read the rules

- [references/guideline.md](references/guideline.md) in full. It sets the sections, their order, and the rules for each one. Its `@` names refer to the files in the table under "Using this skill".
- Hang's past reviews in full, when they are available (see the table under "Using this skill"). They show Hang's voice and the recommendation template. Without them, use the recommendation form in [references/review-voice.md](references/review-voice.md).
- [The portable writing style guide](../writing-style-portable/writing-style-guide.md), starting with Core philosophy, Which guidance wins, and Quick reference, then the sections on claims, sentence style, and editing. Use its companion [vocabulary.md](../writing-style-portable/vocabulary.md) for word choice. Consult [field-conventions.md](../writing-style-portable/field-conventions.md) only when useful for the document being reviewed; dissertation structure and casual slide language do not set the style of a peer review.
- [references/review-voice.md](references/review-voice.md), which distills Hang's reviewing voice and the sentence rules from the style reference that apply to reviews.

The portable guide and the review voice cover ordinary reviews. The full references are large source libraries; open them only when Hang disputes a wording choice, when the portable guide states a rule without the case at hand, or when Hang asks. Then search [Hang's full reference](../writing-style/writing_style_ref/hangs-writing-style-reference.md), or [the committee reference](../writing-style/committee_style_ref/committee-writing-style-reference.md) for conventions of published writing, by heading or pattern name, and read only the entries needed. Hang's current instructions and the portable guide take precedence over older examples that are casual, verbose, or heavily first person. Use "the authors", the method, or the results as the subject by default. Keep first person for a personal concern, a recommendation, or the requested expertise caveat.

### 6. Draft the five sections

Follow the venue form and the review scope the user requested. Use the page and its file-building steps for a full interactive review or when the user requests that surface. The page groups points as summary, strengths, weaknesses, areas for improvement, and recommendation, with IDs such as `summary-1` and `weakness-1`. Use the suggested counts and lengths as pacing defaults. For a focused review, deliver the requested feedback with the same evidence standard.

Follow the current user's reviewing voice and the venue requirements. Treat Hang's past reviews, wording examples, and section lengths as defaults when that style is requested or fits the task. Use the venue's recommendation and confidence fields. Include a statement about the reviewer's expertise only when it is accurate and the user or venue calls for it. Run the file-building steps below for an interactive review.

**Summary** (one or two short paragraphs, usually 80 to 140 words total). Summarize the contributions fairly instead of repeating the claimed list. The first paragraph names the problem, approach, and central contribution in about three sentences. Merge claims that are one idea at two levels of detail, and omit implementation details. Hang's summaries open with "In this work, the authors proposed ...". A second paragraph, if needed, gives the evaluation setting and main result in **one or two sentences**, usually no more than 45 words. Omit metric lists, secondary timings, and a repeated novelty judgment. If a quantitative comparison is essential, give one comparison with its measure, baseline, and condition.

**Strengths** (about three to six points). Lead with what is new and why it matters, then what the community gains, then rigor and clarity. Usually use one or two sentences and 25 to 60 words per point, with one supporting fact. Explain what an ablation establishes without reciting its rows. Put exact values, extra conditions, equation checks, and related-work comparisons in the evidence. Do not narrate the review process with "I worked through" or "I checked". Generic praise needs the fact that earns it.

**Weaknesses** (usually three to five points, most important first). Focus on what is really insufficient, such as unclear contributions, insufficient evidence for a claimed contribution, or wrong or very outdated methods. Ignore minor issues, such as typos, one more sensitivity sweep, or a missing citation that changes nothing. Use at most two points of the "more baselines" or "more recent work" kind. Use a restrained and considerate tone, usually two or three sentences and 40 to 90 words. Each point states one concern, gives one decisive example and location, explains which claim it affects, and briefly says what would resolve it. In an external review, a concern that rests on an observation the paper does not explain ends with a question or a request for clarification, not a guess at the cause. Credit what the authors already did or acknowledged. Move additional examples and calculations into the evidence. Use "My largest concern is" only for the point that really drives the recommendation.

**Areas for improvement** (one or two points, usually two or three sentences each). Say what would make the paper better or acceptable with a reasonable workload. This is different from a weakness. A weakness says what is missing or wrong, and an improvement says what to do. Prefer actions that use what the authors already have, offer a cheaper path next to an experiment (such as narrowing a claim), and mark larger work as optional. Do not claim that a piece of work is small unless the paper shows it. Prefer "Please clarify ..." or "The comparison would be stronger with ..." to conversational requests such as "I would love to see ...".

**Overall recommendation** (one paragraph). Follow the template in Hang's past reviews, or the form in [references/review-voice.md](references/review-voice.md) without them. Briefly summarize the review, give the recommendation and its reason in one short sentence, and end with the not-an-expert line, such as "While I am not an expert in this field, I would not argue if my recommendation largely disagrees with other reviewers'." Read and use the venue's own decision options. If the recommendation is a judgment call, say so in its evidence and name the alternative.

Categories (subheadings) are optional on strength, weakness, and improvement points, and only when the section has three or more points. The template header lists the category names.

### 7. Write the evidence for each item

Every item gets one to five evidence objects, which the page shows right after the point.

- `where`: the location, such as "Sec. III, p. 4" or "Table 2, ablation rows, p. 5".
- `text`: what the paper (or the external source) says. Quote exactly, in quotation marks, when quoting.
- `reason`: the step from the evidence to the wording of the point. Put calculations, assumptions, equation checks, detailed ablation values, search limits, and reference-review decisions here, so the review text stays short. Retain these details when shortening a point.
- `source`, which the Connections tab counts:
  - `claims`, `method`, `experiments`, `baselines`, or `presentation` for the paper
  - `prior` or `field` for external work, with `url`
  - `guideline`, `previous`, or `style` for Hang's rules
  - `summary`, `strength`, `weakness`, or `improvement` for another part of this review

The first evidence of a point is usually the paper location that supports it. Cite a rule (`guideline`, `previous`, `style`) only when it explains a choice Hang might question, such as a dropped reference-review point or a hedged word.

### 8. Draft the state machine of the paper

Map the central claim, the work that carries it, and the evidence that supports it. Give each link a location and mark where the support is partial. In the full interactive review, use the optional `machine` schema in the [format example](review-tools/review-data-format.md) when its experiment-oriented labels fit the paper. Otherwise show these links in the evidence under each review point and omit the machine. If the checker flags the omitted map, verify that the per-point evidence supplies the links.

### 9. Verify before writing the files

Reread the draft as a skeptical co-reviewer who wants to catch you being unfair or wrong. Go through the checklist at the end of [references/calibration.md](references/calibration.md). In short, search `paper.txt` for every quote, check every number against the table image, grep for synonyms before writing that the paper does not state something, redo every calculation, make sure each request is proportionate to the recommendation, and, in an external review, make sure no point explains an observation on the authors' behalf. When a point fails, fix or drop it. Do not keep a flawed point and hope Hang catches it.

### 10. Write the files and check them

1. Create `<paper>/review/` and copy `<review-template>/review.html` into it unchanged. Copy it fresh each time, since the template is the newest version of the page.
2. Write `<paper>/review/review-data.js`. The first line is a comment naming the venue and submission. Then `window.REVIEW_DATA = { ... }` and a final line with `;`. The part from the first `{` to the last `}` must be strict JSON (double quotes, no trailing commas, no comments), because `writing_style_ref/tools/import_review.py` reads it that way. Set `review_id` to a unique identifier derived from this paper's title or submission id, `generated` to the current local time, and `paper` to `{ "id": ..., "venue": ... }` from the supplied manuscript and review form.
3. Run the checker and fix every error. Read each warning and fix it unless there is a reason not to.

```
node SKILL_DIR/review-tools/check_review.js <paper>/review/review-data.js
```

4. Open the page for Hang, for instance with `Start-Process "<full path>\review.html"` in PowerShell.

### 11. Hand off and wait

Tell Hang, briefly:

- the path of the page, and how many points each section has;
- the recommendation, and the alternative if it was a judgment call;
- the two or three points you are least sure about, so Hang can start there;
- which reference-review points were dropped, in one line;
- for venue form fields the page does not hold (scores, confidence, questions for the authors, limitations), suggested values in the chat if the venue's form needs them, following how Hang filled them in the past reviews, when available;
- if the venue form asks about LLM use in reviewing, that the answer there needs to reflect how this draft was made.

Then wait for Hang's decisions. Do not write a final review elsewhere or submit anything.

## Revising after Hang's decisions

The page's "Save decisions" button writes `<review_id>.decisions.<timestamp>.json` into the page's own folder, `<paper>/review/`, in browsers that can write to a folder (Chrome and Edge). The first save asks Hang to choose that folder once. In other browsers, or when Hang picks "Download instead", the file goes to Downloads. When decisions are saved, read every batch for this `review_id` in time order from the review folder and any matching files in Downloads. Use the latest explicit decision for the current version of each point.

- Keep an accepted point as saved. Use `revised_override` as the user's wording and retain the point's saved status. Keep pending and rejected points out of the approved review. Resolve an attached condition before using the point in a final review. Use `comment` and top-level `notes` as instructions for the applicable points, keeping factual or venue conflicts visible. When a `verdict: "disagree"` challenges evidence, recheck it and fix or drop the point.
- Give edited wording more weight than acceptance without edits. Infer pending decisions only when the user delegates them, and record the resulting decisions and reasons as delegated. Preserve verified claims, data, citations, and terms. Reopen a rejected point when the user requests a rework or supplies material new evidence, and explain what changed.
- Keep the ids of unchanged items. The page drops a saved decision only when the text or evidence under it changes, so unchanged points keep their decisions.
- Update `generated`, rerun the checker, and summarize what changed.
- Hang's edits and rejections are the strongest evidence of the reviewing style. If Hang wants them kept, offer to move a decisions file that went to Downloads into the review folder and run `python SKILL_DIR/../writing-style/writing_style_ref/tools/import_review.py <paper>/review`, and ask before running it, since it changes the shared style reference. An installed copy is replaced on the next install or plugin update, so run it on the repository's copy (`plugins/review-your-papers/skills/writing-style/`) and update the installation.

## Edits to a manuscript's text

When Hang asks for edits to a document whose source you have, rather than a review (for example Hang's own paper, a co-authored draft, or the text of a response to reviewers), propose them on the editing page so that Hang accepts, rejects, or rewords each one before it is applied.

1. Read `<edit-template>/README.md`. It specifies `items.txt`, the page, the decision batches, and the apply step. Its scripts run on Python 3.8 or newer.
2. Create a review folder inside the document's root folder, such as `<root>/waiting_for_review/<name>/`. Record Hang's request word for word as `@request R1`. Give each edit an `@item` with `type:`, `file:` relative to the root, a `note:` with the reason and the style principle it follows, and `--- original` copied exactly from the source with its `--- revised`. Add `markers: no` under `@review` unless the document defines the `\hang` macro and Hang wants the changes tagged.
3. Word the edits with the portable writing style guide, especially "7. Editing someone else's text": correct without rewording, keep a correction separate from optional rewording, and check earlier decisions before proposing a change again.
4. Build with `python <edit-template>/build_review.py <root>/waiting_for_review/<name> --root <root>`, fix any passage reported as missing or not unique, and open `review.html` for Hang.
5. After Hang saves a batch, run `python <edit-template>/apply_decisions.py <root>/waiting_for_review/<name> --root <root>` as a dry run, and run it with `--write` when Hang confirms. Only accepted items are applied, with Hang's wording where it was edited.
