# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1. Overall test accuracy floor across all seeds

<!-- Your criterion. It must name a number. -->

Across the 15% test split, the model achieves an overall accuracy of at least **0.72** across all three seed runs (seeds 42, 7, and 2024).

**Why this target:**
In `r/nba` discussions, informal slang and short posts create baseline noise, but structured text patterns remain distinct. A three-class random guesser gets 33% and a majority guesser gets ~45%; setting a hard floor of 0.72 across all three seeds ensures the model reliably distinguishes objective basketball text from fan noise without being sunk by a bad split draw.

---

## 2. Per-label F1 for `analysis`

<!-- Your criterion. -->

The `analysis` label achieves an F1 score of at least **0.65** on the test set across all three seed runs.

**Why this target:**
`analysis` is the hardest class to collect in a fast-moving sports sub (representing only ~45 of my 200 posts, or roughly 6–7 posts in each test split). Because a single misclassification drops the test F1 score for `analysis` by ~10–12 points, a 0.65 target reflects high classification precision while accounting for small-sample metric volatility across seeds.


---

## 3. Class balance dominance ceiling

<!-- Your criterion. -->

No single label in `labels.csv` represents more than **55%** (110 out of 200 posts) of the total dataset, and every label contains at least **40 posts**.

**Why this target:**
Sports forums naturally over-index on short game-night reactions. Capping `reaction` at 55% prevents DistilBERT from exploiting a majority-class shortcut (guessing `reaction` constantly to drive loss down) while guaranteeing at least 40 examples for rarer classes like `analysis`.
---

## 4. Confidence-to-accuracy gap

<!-- Your criterion. -->
The accuracy on the top third most confident predictions (highest softmax probabilities) is at least **0.15 (15 percentage points)** higher than the accuracy on the bottom third least confident predictions.

**Why this target:**
Because `hot_take` and `reaction` share heated, emotional vocabulary, the model will naturally be less certain along that boundary. Requiring a 15-point accuracy gap ensures that high softmax confidence actually correlates with clear-cut posts rather than arbitrary guesswork.

---

## 5. Classification precision on anti-LeBron GOAT claims

<!-- Your criterion. -->

Posts arguing against LeBron James being the GOAT that are labeled as `hot_take` achieve a precision of at least **0.70** across held-out test splits.

**Why this target:**
Debates surrounding why LeBron should not be considered the GOAT (e.g., citing Finals record, 2011 Dallas series, or clutch execution) frequently blur the line between a structured `analysis` post and an unsupported `hot_take`. Requiring 0.70 precision on these specific claims ensures the model doesn't falsely classify emotional anti-LeBron rants as objective statistical analysis.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
