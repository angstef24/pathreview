## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/147

**Issue title:** Resume section detection fails on text with leading whitespace
 #147

**Tier:** [X] Tier 1  [ ] Tier 2  [ ] Tier 3

**Problem summary:**
The bug lies in _detect_sections() in resume.py. Section-header detection only matches headers at the start of a line, so when a PDF adds leading whitespace (indentation) before a header, it's missed—causing the parser to conclude the resume has no sections at all. The fix needs to recognize indented text as a valid header while still not treating every indented line as one.

**Selection Reasoning:**
I chose this issue because this falls within my limited scope since I have never done big codebase changes or fixes. In the past, I have taken python classes that taught the basics of regex and cleaning data with abnormalities like whitespaces. Data cleaning is also a relevant topic in my job, so I feel that it is a topic I am well versed with.

**Branch name:** fix/147-resume-section-detection-fails-on-text-with-leading-whitespace

**Setup confirmation:** [X] App runs locally at localhost:5173

**Cohort ledger:** [X] Issue added to cohort ledger


## Week 8 — Reproduction & solution planning

**Reproduction commit link:** https://github.com/angstef24/pathreview/commit/b8d7682

**Reproduction summary:**
I called `ResumeParser._detect_sections()` directly with two versions of the same resume text — one flush-left, one with each line indented (mimicking how some PDFs extract text with a left margin). The flush-left version correctly returned `['Experience', 'Skills', 'Education']`, while the indented version returned `[]`. This confirmed the existing `tests/unit/test_resume_parser.py::test_detect_sections` test, which also fails today (`assert 0 > 0`) because its sample text is indented — the regex patterns in `_detect_sections()` anchor directly on `^`/`\n` with no allowance for leading whitespace.

**PLAN.md link:** https://github.com/angstef24/pathreview/blob/fix/147-resume-section-detection-fails-on-text-with-leading-whitespace/PLAN.md

**Blockers or open questions:**
Still need to confirm whether real-world PDF extraction ever uses tabs or non-breaking spaces for indentation (vs. plain spaces), which would affect how permissive the fixed regex needs to be.


## Week 9 — Solution building & PR submission

### Check-in 1 (mid-week)

**Current progress:**
The fix from PLAN.md is implemented. I rewrote the pattern list in `_detect_sections()` (`ingestion/parsers/resume_parser.py`) so the header regexes use `^[ \t]*`/`\n[ \t]*` instead of anchoring directly on `^`/`\n`, which lets indented and PDF-extracted headers (e.g. `    Education:`) match.

Completed PLAN.md sub-tasks:
1. Fixed `_detect_sections()` to allow leading whitespace before a header.
2. Confirmed the three originally-failing tests now pass: `test_detect_sections`, `test_parse_single_column_resume_text`, `test_parse_resume_no_work_experience`.

**Next steps:**
Add/update unit tests covering the indentation fix (per PLAN.md's sub-tasks 3–4: an indented-header case and a guard against misdetecting indented body text as a header), run `make check` on the changed files and fix any issues, open a draft PR against pathreview and request peer feedback in Slack, fill in the PR template, then mark it ready for review and add Check-in 2.

**Blockers:**
No major blockers. It may take some time to get the PR reviewed. Note: `make test-unit` has ~50 pre-existing failures across unrelated modules (`review_service`, `pii_scrubber`, `skill_extractor`, etc.) that exist on a clean checkout before my changes. My changes don't touch those; I confirmed the resume-parser suite goes from 5 failing to 2 failing, where the remaining 2 (`test_parse_markdown_resume`, `test_strip_markdown_syntax`) are pre-existing failures in `_strip_markdown`, unrelated to issue #147.

---

### Check-in 2 (end of week)

**PR link:** https://github.com/ascherj/pathreview/pull/723

**Branch:** fix/147-resume-section-detection-fails-on-text-with-leading-whitespace

**What you built:**
Updated the four regex patterns in `_detect_sections()` (`ingestion/parsers/resume_parser.py`) to allow optional leading whitespace (`[ \t]*`) before a section header, so resumes with indented/PDF-extracted headers (e.g. `    Experience`) are correctly detected instead of returning no sections at all.

**Tests added or updated:**
Added `test_detect_sections_with_leading_whitespace` and `test_detect_sections_does_not_match_indented_body_text` to `tests/unit/test_resume_parser.py` — one confirms indented headers are detected, the other guards against indented body text that merely mentions a header word being misdetected as one.

**Self-review confirmation:** [X] make check passes  [X] make test-unit passes
(Both "pass" in the sense that my changes introduce no new failures beyond this repo's pre-existing baseline — documented in the PR description: `make test-unit` goes from 53 failing/375 passing to 50 failing/380 passing, and `make lint` goes from 182 to 177 pre-existing errors; `make typecheck` is unaffected.)

**Draft PR feedback received from:** none yet

**Blockers or open questions:**
None currently. The `_strip_markdown()` header-stripping bug (same "no leading whitespace" root cause, different method) is still unfixed and causing 2 pre-existing test failures — flagged in the PR as a possible follow-up issue, out of scope for #147.
