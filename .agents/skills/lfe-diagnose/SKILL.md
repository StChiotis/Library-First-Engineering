---
name: lfe-diagnose
description: Disciplined bug-diagnosis loop. Reproduce → Hypothesise → Fix.
---

# LFE Diagnose

## Process
1. **Feedback Loop**: Build a deterministic repro (test/script).
2. **Reproduce**: Run the loop and witness the failure.
3. **Hypothesise**: Generate 3-5 ranked, falsifiable hypotheses.
4. **Instrument**: Add targeted probes to test hypotheses.
5. **Fix**: Write regression test → Apply fix → Watch pass.
