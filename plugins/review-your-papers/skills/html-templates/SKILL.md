---
name: html-templates
description: Build interactive HTML review pages for scholarly work. Use the editing template to stage source passages for approval and the review template to present peer-review points with evidence. The editing helper matches UTF-8 text such as LaTeX, Markdown, or plain text. Use a format-specific workflow to edit Word or other non-text sources.
---

# HTML templates

The `advisor-review-paper` and `review-paper` skills use these two templates. They were moved here from Hang's dissertation and review folders, and these copies are the ones the skills use.

| Template | Use |
| --- | --- |
| [edit_template](edit_template/README.md) | Proposed edits to a source file. The author accepts, rejects, or rewords each one on a page, and `apply_decisions.py` writes the accepted ones into the source. |
| [review_template](review_template/) | A review report (summary, strengths, weaknesses, areas for improvement, recommendation, and a map of the paper's argument) with evidence under each point. The header comment of `review.html` (its first 116 lines) specifies `review-data.js`. |

## Editing template

Read [edit_template/README.md](edit_template/README.md) first. It specifies `items.txt`, the page, the decision batches, and the apply step, and `edit_template/example/` is a small review to try it on.

Put the review folder with the paper and pass its source root explicitly. Use UTF-8 text with a unique before passage or insertion anchor. For non-LaTeX sources, inspect the exact-source view to verify markup. Set `markers: no` for clean source and for files that do not support the annotation macro. Use markers only when the source supports them and the author wants them.

```bash
python <this skill>/edit_template/build_review.py <root>/waiting_for_review/<name> --root <root>
python <this skill>/edit_template/apply_decisions.py <root>/waiting_for_review/<name> --root <root>
python <this skill>/edit_template/apply_decisions.py <root>/waiting_for_review/<name> --root <root> --write
```

Treat the build and second command as validation. Check overlaps, dependencies, and any conditions attached to an acceptance. Use the third command only for authorized, accepted edits and verify the resulting document. Use check mode for approval of a transcription or other source reading. Its decisions record readings and carry no source edits. Offer a second wording option only when the choice matters to the author. For a requested second look, use the option format in [second-agent-brief.md](edit_template/second-agent-brief.md) with the current paper's source format, root, style references, and validation steps.

## Review template

Copy `review_template/review.html` unchanged into the output folder, next to a `review-data.js` written to the specification in its header comment. Copy it fresh each time, since the template is the newest version of the page. The review skills each carry `review-tools/check_review.js`, which checks a `review-data.js`, and `review-tools/review-data-format.md`, a worked example of that file.

The page's Save decisions button writes `<review_id>.decisions.<timestamp>.json` into the page's own folder, next to `review-data.js`, in browsers that can write to a folder (Chrome and Edge). The first save opens a dialog with the folder's path to paste into the folder picker, and the page refuses a folder that does not hold this review's `review-data.js`. The choice is remembered for that page, so later saves write directly, and after a browser restart the browser may ask once for permission again. In other browsers, or with Download instead, the file goes to Downloads.

Keep review folders, decisions, and outputs with each document, never in this skill. An installed copy is replaced on the next install or plugin update, so make lasting changes to a template in the repository.
