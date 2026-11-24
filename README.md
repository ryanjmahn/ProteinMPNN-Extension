<div align="center">

# ProteinMPNN-Extension

Contaminated Ancestors: Seven Ways Our Own Evaluation Protocol Nearly Told Us the Wrong Thing

We built an AI tool to catch errors in reconstructed ancient proteins, then checked whether it actually worked. It didn't, and our own testing process nearly hid that.

Experiment is complete · Co-Authored Paper Accepted @ NeurIPS 2026 Workshop: Trust-AI-Eval (TAE) - only all-high-school author team out of 226 accepted TAE papers (0.44%) · [Read the paper](docs/paper.pdf)

</div>

Built an AI tool to catch errors in reconstructed ancient proteins, then tested whether it actually worked. Across 120 trials on 40 protein families, a simple text-matching method beat our AI (90% vs 28%). Found 7 flaws hiding in our own testing process and co-wrote an 11-point checklist so other labs catch theirs.

## The problem

Scientists rebuild ancient proteins by working backward from the proteins alive today. The method assumes every protein has a single family line. When two lines have mixed, it quietly returns a "chimera": one stretch from one ancestor stitched to a stretch from another, and it still reports high confidence (93% average confidence at only 69% accuracy).

We tried to catch these stitched-in stretches with ProteinMPNN, an AI model of protein structure, then tested it on simulated protein families where we knew the right answer.

## The result

| Method | Caught the error | 95% range |
|---|---|---|
| Simple text-matching (no AI) | **36/40 (90%)** | 77–96% |
| ProteinMPNN (our AI) | 11/40 (28%) | 16–43% |
| ProteinMPNN with structure scrambled | 2/40 (5%) | 1–17% |

- **The simple method wins clearly.** The ranges don't overlap.
- **The AI's small signal is real.** Scrambling the protein structure removes it.
- **At 6 trials we couldn't tell.** It looked like 4/6 vs 2/6. It took 40 to be sure.

## 7 flaws in our own testing

| # | Flaw | What it did |
|---|---|---|
| 1 | The main score measured the wrong thing | Real errors and fake "no-error" data were flagged at the same rate (2/15 each). Only *where* the flag landed told them apart (overlap 0.81 vs 0.08). |
| 2 | A sign rule flipped a metric | Our best run scored 0.048 when the truth was 0.952. |
| 3 | The "random guess" baseline was wrong | We assumed 0.5. The real baseline was 0.50–0.68. |
| 4 | Too few trials | A control looked perfect at 0/6, then misfired at a normal rate (2/12) with more trials. Random variation was bigger than any effect we studied. |
| 5 | A hidden confound | The comparison secretly also changed how much data each side used (12 vs 6 sequences). Controlled, the effect went to zero. |
| 6 | An unfair competitor test | The rival tool (GARD) was run in a setup that hurt it, which we hadn't said. |
| 7 | A summary of a run that didn't exist | An internal results file reported data that was never saved and couldn't be rebuilt. We deleted it. |

**Bonus:** Apple's GPU backend silently corrupted numbers mid-run, turning p = 0.030 into p = 1.000 with no error. Every reported number was rerun on CPU.

None of these needed biology. Any team testing a detector against a known answer can hit all of them.

## The 11-point checklist

Our own work broke 9 of the 11 (†).

1. † Does your main score actually measure what you claim?
2. † Do you report both *whether* it fired and *where*?
3. † Have you measured each metric's real baseline on fake data, not assumed it?
4. † Does any metric depend on a convention the answer key never checks?
5. † Does a clean control stay clean with more trials?
6. † Are your "independent" trials actually independent?
7. † Does anything else change along with your treatment?
8. † Is your competitor set up at a disadvantage you haven't disclosed?
9. † Can every reported number be rebuilt from saved data today?
10. Would you notice if the hardware corrupted your numbers?
11. Have you swapped the AI for a simple non-AI version in the same pipeline?

Item 11 was our most valuable experiment. It took minutes and showed the power came from an earlier step, not the AI.

*Limitations: one pipeline in one field, audited by its own authors. Some corrected numbers still rest on 2–3 trials and are marked suggestive.*

## Stack

Python · PyTorch · ProteinMPNN · IQ-TREE · MAFFT · HyPhy (GARD) · ColabFold

## Citation

```bibtex
@inproceedings{huang2026contaminated,
  author    = {Christopher Yu-Hao Huang and Ryan Jaemin Ahn and Wenhao Lu},
  title     = {Contaminated Ancestors: Seven Ways Our Own Evaluation Protocol
               Nearly Told Us the Wrong Thing},
  booktitle = {NeurIPS 2026 Workshop on Trust-AI-Eval (TAE): Can We Trust AI Evaluation?},
  year      = {2026}
}
```

Data: simulated protein families on one shared backbone (PDB 5L33, chain A), plus one real protein family.
