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

I collected comments from old.reddit.com/r/nba, pulling from multiple threads across different days and topic types — not just one day's news cycle — to avoid skewing toward a single event. Specifically:
- On-court/performance/roster threads (trade speculation, player evaluation, game recaps) for `analysis` and `hot_take` examples
- Off-court/controversy threads (parades, fan incidents, league news) for `reaction` and `hot_take` examples

Final dataset: 200 labeled examples across three labels — analysis (66), hot_take (64), reaction (70). No label exceeded 35% of the dataset, well within the 70% cap requirement.

## Evaluation Metrics

I reported overall accuracy for both models, but accuracy alone can hide a model that just predicts the majority class well and ignores minority labels. I also reported per-class F1, since it balances precision and recall and gives one number per label that reflects whether the model is actually learning that label's boundary rather than defaulting to it. The confusion matrix revealed directional errors — specifically whether `analysis` and `hot_take` get confused with each other, which was the boundary I anticipated being hardest given my edge case.

## Definition of Success

I defined success as per-class F1 ≥ 0.65 on all three labels and outperforming the zero-shot Groq baseline by at least 10 percentage points in overall accuracy. The fine-tuned model did not meet this threshold. The hot_take label had F1 = 0.00 on the test set, and the overall fine-tuned accuracy (56.7%) was lower than the baseline (83.3%). This is documented and analyzed in the README evaluation report.

## AI Tool Plan

* **Label definition review: Before starting the full annotation process, I personally reviewed a sample of Reddit comments and developed my labeling guidelines. I used an LLM as a secondary check to test whether my label definitions were clear and whether any edge cases needed clarification. Based on this feedback, I refined my guidelines before manually labeling the full dataset.
  
* **Annotation workflow support: I collected and labeled the dataset myself using the defined criteria. For a small subset of examples, I consulted an LLM to compare interpretations of difficult cases. The AI output was only used as a reference point; I manually reviewed every example and made the final labeling decision based on my own judgment.
 
* **Error analysis support: After training the model, I analyzed the evaluation results and confusion matrix to understand where the model struggled. I used an LLM as a brainstorming aid to help organize possible error patterns, then manually verified those patterns by reviewing the original examples. The analysis showed that the model correctly classified analysis (10/10) and reaction (7/10), but struggled with hot_take (0/10), often confusing hot_take examples with analysis (9/10 misclassified as analysis). I confirmed this behavior through my own review before including it in the evaluation report.

## Post-Training Notes

The fine-tuned model did not meet the success criteria defined above. The hot_take label had F1 = 0.00 on the test set, driven by the model collapsing to a near-binary classifier (analysis vs reaction) and never predicting hot_take. This signals that the boundary between hot_take and analysis was too subtle for DistilBERT to learn reliably from 64 training examples — hot_take sits between the other two labels in terms of evidence and structure, making it the hardest to separate with limited data.

The baseline (Groq llama-3.3-70b-versatile) outperformed the fine-tuned model at 83.3% vs 56.7%. This is itself an informative result — it shows that a large pretrained model with well-defined label definitions in the prompt can outperform a small fine-tuned model on limited, subjectively labeled data. More training examples, tighter label definitions, or a larger base model would likely close this gap.
