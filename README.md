# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->

This classifier analyzes public discussion posts from `r/nba` centered around player performance and career legacy. It categorizes text into three distinct writing styles: tactical **analysis**, sweeping **hot_take** commentary, and immediate game **reaction** text. By training on these specific patterns, the model automatically separates objective basketball breakdowns from emotional fan discourse.

---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `analysis`
* **Definition:** Makes an argument backed by a specific, checkable fact. A stat, a date, a quote, or a tactical tracking metric.
* **Example 1:**
  > "Looking at the tracking data, the defense ran drop coverage on 74% of pick-and-rolls, which completely opened up the mid-range."
* **Example 2:**
  > "His true shooting percentage on isolation plays has actually held up at 58% over the last three seasons."

### `hot_take`
* **Definition:** A confident, sweeping claim with no support offered. Might be true, but isn't argued or backed by data.
* **Example 1:**
  > "He is a certified playoff choker and an absolute fraud who can't win without a superteam."
* **Example 2:**
  > "He is the undisputed GOAT and anyone who says otherwise doesn't actually understand basketball."

### `reaction`
* **Definition:** An immediate response to something that just happened in the game. Pure feeling, not a structured argument.
* **Example 1:**
  > "We are literally the worst franchise in the history of sports. Fire the coach and blow it all up right now."
* **Example 2:**
  > "He completely checked out on defense in the final two minutes tonight and looked totally exhausted."

### The hardest boundary

**Which two labels:**

**The decision rule I used every time:**
<!-- e.g. "If the post names a specific checkable fact, it's `analysis`, even
     if the tone is heated." -->
     **The Hardest Boundary:** Between `hot_take` and `reaction`. Both lack statistical evidence and express strong opinions.

**Decision Rule:** 
If the text is an immediate, in-the-moment emotional outburst tied to tonight's specific game or quarter (e.g., *“checked out in the final two minutes tonight”*), it is a **reaction**. If it's a sweeping, generalized statement about a player's career or legacy that could be posted at any time of the year regardless of tonight's score (e.g., *“can't win without a superteam”*), it is a **hot_take**.

---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:**

Public discussion posts collected from r/nba threads focusing on LeBron James and basketball commentary.

**How I labelled them:** <!-- Cold first? Pre-labelled with AI and corrected?
Say so plainly — the disclosure is required, not penalised. -->

I labeled the first 20 posts completely cold (manually, without assistance or external tools) to establish my baseline judgment, marking them as cold in the note column. The remaining 180 posts were pre-labeled using an AI model seeded with my taxonomy definitions, after which I manually read, verified, and corrected every single row before marking them as pre-labeled.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `analysis` | 68 | 34.0% |
| `hot_take` | 67 | 33.5% |
| `reaction` | 65 | 32.5% |
| **Total** | 200 | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:* "He scored 38 points tonight, but he completely checked out on defense in the 4th quarter like he always does when the pressure rises."
>
> *Could have been:* `analysis` or `hot_take`
>
> *I chose, because:* I labeled it `hot_take`. Although it opens with a checkable stat (38 points), the single statistic is used purely as flavor text for a sweeping, unsupported narrative about his general career character ("like he always does").

**2.**
> *The post:* "Looking at the fourth quarter play-by-play, he missed 4 straight shots in the final 3 minutes and turned it over twice. He is a fraud who shrinks when the lights get bright."
>
> *Could have been:* `analysis` or `hot_take`
>
> *I chose, because:* I labeled it `analysis`. The core argument relies on specific, checkable play-by-play evidence from that game (4 missed shots and 2 turnovers in the final 3 minutes) as structural proof, satisfying the analysis decision rule despite the heated concluding tone.

**3.**
> *The post:* "We lost by 25 points tonight because he refuses to play transition defense anymore and just complains to the refs."
>
> *Could have been:* `hot_take` or `reaction`
---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:**
`distilbert-base-uncased` (passed via `BASE_MODEL`)

**Settings:** <!-- epochs, learning rate, batch size, seed -->
* Epochs: 3 (passed via `EPOCHS`)
* Learning rate: $2e-5$ (passed via `LEARNING_RATE`)
* Batch size: 16 (passed via `BATCH_SIZE`)
* Seed: 42 (passed via `SEED`)

**Anything I changed from the defaults, and why:**
None. I kept all training settings and hyperparameters at their standard notebook defaults.

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->
* Train / Val / Test split: **train 139 · val 31 · test 30**
* Per-label counts across splits:
  * `analysis`: train 46, val 11, test 10
  * `hot_take`: train 46, val 10, test 10
  * `reaction`: train 47, val 10, test 10
* No label falls below 8 in the test split (each has exactly 10).
---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:* A classification pass on the raw `r/nba` dataset posts using my custom taxonomy definitions for `analysis`, `hot_take`, and `reaction`.
- *What came back:* A batch of pre-labeled rows with initial category predictions.
- *What I changed:* Read through, verified, and manually corrected every single pre-labeled row to ensure strict adherence to the decision rules and edge-case criteria.

**Moment 2**

- *What I asked for:* An explanation and breakdown of my model's training results and epoch metrics.
- *What came back:* An explanation of how validation loss, accuracy, and macro F1 improved across epochs, along with formatted metric tables.
- *What I changed:* Reviewed the explanation to verify model convergence and integrated the structured metrics directly into my project documentation.

**Pre-labelling disclosure:**
I labeled the first 20 rows of the 200-row dataset completely cold to establish my baseline judgment. The remaining 180 rows were pre-labeled using an AI model seeded with my taxonomy definitions, and I subsequently read, verified, and manually corrected every single row before finalizing `labels.csv`.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.600 | 0.933 | +0.333 |
| Macro F1 | 0.489 | 0.933 | +0.444 |
| F1 — `analysis` | 0.667 | 0.909 | +0.242 |
| F1 — `hot_take` | 0.000 | 0.889 | +0.889 |
| F1 — `reaction` | 0.800 | 1.000 | +0.200 |


**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

     I predict that the fine-tuned DistilBERT model will outperform the baseline primarily by accurately identifying hot_take examples that the baseline failed to capture entirely, while maintaining stronger distinction across nuanced analysis and reaction posts.


**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->

     The huge jump from a 0.489 Macro F1 to 0.933 shows that fine-tuning was entirely necessary. The baseline failed completely on hot_take (0.000 F1), meaning simple rules couldn't spot exaggerated or provocative posts. Fine-tuning DistilBERT fixed this completely—boosting hot_take to 0.889, hitting 1.000 on reaction, and making the model consistently accurate across all three classes.

---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->


Accuracy moved 0.033 across seeds. Stable enough to trust.

| Criterion                                              | Target   | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall test accuracy floor across all seeds        | ≥ 0.72   | 0.933   | 0.967  | 0.933     | PASS    |
| 2. Per-label F1 for `analysis`                         | ≥ 0.65   | 0.909   | 0.952  | 0.952     | PASS    |
| 3. Class balance dominance ceiling                     | ≤ 55%    | 35.0%   | 35.0%  | 35.0%     | PASS    |
| 4. Confidence-to-accuracy gap                          | ≥ +0.150 | +0.200  | +0.200 | +0.200    | PASS    |
| 5. Classification precision on anti-LeBron GOAT claims | ≥ 0.70   | 0.889   | 0.947  | 0.889     | PASS    |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted | analysis | hot_take | reaction |
|---|---|---|---|
| **analysis** | 10 | 0 | 0 |
| **hot_take** | 2 | 8 | 0 |
| **reaction** | 0 | 0 | 10 |

Biggest confusion: 2 real `hot_take` posts called `analysis`.
The other direction: 0 real `analysis` posts called `hot_take`.

Roughly symmetric — the model finds this pair hard in both directions,
which more often means the boundary itself is genuinely hard.

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->

* **Biggest confusion:** 2 real `hot_take` posts were misclassified as `analysis`.
* **The other direction:** 0 real `analysis` posts were misclassified as `hot_take`.

**Directional takeaway:** This error is directional rather than symmetric. When the model hesitates between a provocative claim and structured reasoning, it defaults toward `analysis` (likely due to length or technical basketball vocabulary in those specific anti-LeBron GOAT posts), rather than over-calling `hot_take`.

---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** ___ / 30 = ___%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
