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
