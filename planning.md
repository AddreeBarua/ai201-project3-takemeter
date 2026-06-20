# TakeMeter — Planning

## Community

I chose r/nba because it's one of the largest and most active sports communities online, with discourse that ranges from rigorous stat-based argument to pure emotional reaction — sometimes within the same thread. Roster/performance threads (trades, player evaluations) tend to produce more analysis-style comments, while off-court or controversy threads (parades, politics, fan behavior) skew toward hot takes and pure reactions. This mix is exactly what makes a discourse-quality classifier meaningful here: a regular in this community can immediately tell the difference between someone making a real argument and someone just venting, and that distinction matters for how seriously a comment is taken.

## Labels

- **analysis**: The post makes a claim backed by a specific, checkable fact — a stat, a score, a roster/cap detail, or a verifiable game event.
  - Example: "He can't really space the floor. Too few attempts and a low 30's shooting %. Great rim protector and tenacious rebounder are his reputation, not what he really is since last season."
  - Example: "Spurs have no big man depth we had two centers in the rotation and a bunch of 3s playing up to the 4 spot. And one of those centers took 1 3 all year."

- **hot_take**: A confident, declarative opinion stated with no real evidence behind it — the claim is just asserted, not argued.
  - Example: "Spurs should trade for Giannis, would solve like 90% of their issues."
  - Example: "No exaggeration, he has to be one of the biggest wastes of athletic talent of all time."

- **reaction**: A short emotional or joking response with no argument at all — a pure gut reaction to a moment, not a claim about anything.
  - Example: "Ok we'll take Jokic"
  - Example: "Keep Naz out of this"

## Hardest Edge Case

Example post: "I watched him live in PHX, I kid you not, I've never seen a player less engaged in a game. It's even more egregious in person, it was like he was just standing there."

This is hard because it has the *shape* of analysis — specific, detailed, tied to one named occasion — but it's actually a personal, subjective impression that no other reader can check or verify.

**Decision rule:** If the evidence cited is a stat, score, or publicly documented fact, label `analysis`. If it's a personal eyewitness account or "I watched and felt X" — even if detailed and specific — label `hot_take`, since it can't be independently verified by another reader.

## Data Collection Plan

I'll collect comments from old.reddit.com/r/nba, pulling from multiple threads across different days and topic types — not just one day's news cycle — to avoid skewing toward a single event. Specifically:
- On-court/performance/roster threads (trade speculation, player evaluation, game recaps) for `analysis` and `hot_take` examples
- Off-court/controversy threads (parades, fan incidents, league news) for `reaction` and `hot_take` examples

Target: roughly 65–70 examples per label (200+ total, no label over 70%). If a label is underrepresented after an initial pass, I'll target threads more likely to produce it specifically — e.g., box-score/stat-heavy game threads tend to produce more `analysis`, while live game-thread comments tend to produce more `reaction`.

## Evaluation Metrics

I'll report overall accuracy for both models, but accuracy alone can hide a model that just predicts the majority class well and ignores minority labels. So I'll also report per-class F1, since it balances precision and recall and gives one number per label that reflects whether the model is actually learning that label's boundary rather than defaulting to it. I'll also use the confusion matrix to see directional errors — e.g., whether `analysis` and `hot_take` specifically get confused with each other, which is the boundary I expect to be hardest given my edge case.

## Definition of Success

I'll consider the fine-tuned model genuinely useful if it achieves per-class F1 ≥ 0.65 on all three labels and outperforms the zero-shot Groq baseline by at least 10 percentage points in overall accuracy. If the model can't clear F1 ≥ 0.50 on any one class, that's a signal the label boundary itself needs to be revisited rather than just adding more data.

## AI Tool Plan

* Label definition review: Before starting full annotation, I used an LLM as a second opinion to review a small sample of Reddit comments against my labeling guidelines. This helped identify unclear cases, refine label definitions, and improve consistency before manually labeling the full dataset.
* Annotation workflow support: I manually reviewed and labeled the dataset, using an LLM only as an optional reference for a subset of examples where I wanted an additional perspective. Any AI-generated suggestions were treated as recommendations only — I independently checked each example and made the final labeling decision. AI-assisted rows are documented in the `notes` column for transparency.
* Error analysis support: After model training, I plan to use an LLM as a review assistant to help organize misclassified examples and identify possible patterns in model errors. I will manually analyze these cases, verify the reasoning against the original text, and use my own judgment when reporting.
