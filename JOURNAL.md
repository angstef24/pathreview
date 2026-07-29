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

**Reproduction commit link:** PENDING_COMMIT_URL

**Reproduction summary:**
I called `ResumeParser._detect_sections()` directly with two versions of the same resume text — one flush-left, one with each line indented (mimicking how some PDFs extract text with a left margin). The flush-left version correctly returned `['Experience', 'Skills', 'Education']`, while the indented version returned `[]`. This confirmed the existing `tests/unit/test_resume_parser.py::test_detect_sections` test, which also fails today (`assert 0 > 0`) because its sample text is indented — the regex patterns in `_detect_sections()` anchor directly on `^`/`\n` with no allowance for leading whitespace.

**PLAN.md link:** PENDING_PLAN_URL

**Walkthrough video (recommended):** _not recorded_

**Blockers or open questions:**
Still need to confirm whether real-world PDF extraction ever uses tabs or non-breaking spaces for indentation (vs. plain spaces), which would affect how permissive the fixed regex needs to be.
