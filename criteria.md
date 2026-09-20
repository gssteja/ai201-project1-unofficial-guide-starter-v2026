# Acceptance criteria — The Unofficial Guide

Five criteria that say what "working" means for this system, written in unit 1
**before** any results existed.

An acceptance criterion names a target: a number, a count, a rate, or something
a person could plainly observe. *"Retrieval works"* is an opinion. *"For at
least 4 of my 5 test questions, the top results include a chunk containing the
answer"* is a criterion.

Under each one, write a sentence or two on **why that target** and not a
stricter or looser one. A reason that says something about your corpus or your
pipeline earns credit; *"80% seemed reasonable"* does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## 1. Retrieved chunks contain the answer

For at least 4 of my 5 test questions, the retrieved chunks include one that
contains the answer.

**Why this target:**
4 of 5 and not 5 of 5 because one of my questions asks about an "accessible
entrance" and the corpus never uses that phrase — `housing_aldridge_hall.txt`
only says the elevator is out about a week per semester. I expect the wording
gap to cost me that one.

---

## 2. Every answer names a source

Every answer the system produces names at least one source document.

**Why this target:**
All five, because anything the gate lets through already came back with a
filename attached to it — the source is sitting right there in the retrieved
chunk. If an answer comes out without one, the model ignored an instruction it
was given, and that's a bug rather than a near miss.

---

## 3. The relevance gate stops out-of-corpus questions

When I ask a question my documents clearly don't cover, the relevance gate
stops it and the system returns "I don't have enough information about that" —
in at least 4 of 5 tries.

<!-- The five questions are the ones in `OUT_OF_SCOPE` at the bottom of
     `questions.py`, and `run_eval.py` puts them through the gate and writes
     what happened into your run log. Swap them for your own if you'd rather —
     just keep five of them, or the "4 of 5" above has nothing to be 4 of. -->

**Why this target:**
My two groups came out clean apart: in-corpus best distances 0.238–0.475, the
five out-of-scope ones 0.787–0.923. Nothing overlapped, so a cutoff of 0.6 sits
in open space. I still said 4 of 5 rather than 5 of 5 because the nearest
out-of-scope question ("capital of Mongolia", 0.787) is the one an off-topic
question worded more like campus admin could plausibly slip under.

---

## 4. Something about your chunks

<!-- YOU WRITE THIS ONE.

     How would you know if your chunks were the right size? Name something
     countable or observable.

     Examples of the right shape — don't copy these, they should come from
     what you actually saw in Milestone 3:
       - "At least 4 of 5 sampled chunks read as a complete thought, with no
          sentence cut in half at either end."
       - "No chunk is shorter than 200 characters, since anything below that
          in my corpus turned out to be a heading with no content under it." -->

All 5 chunks I sample name their subject in the first line and read as one
complete thought, with no sentence cut in half at either end, and no chunk is
shorter than 60 characters.

**Why this target:**
The starter's chunker produced a 2-character chunk on advice_threads, so the
floor is there to catch that. The first-line rule is the one I actually care
about: `housing_aldridge_hall.txt` covers build year, walk time, the elevator,
laundry prices and noise rules in one post, and split on paragraphs the
elevator line becomes "The bad: the elevator is out roughly one week per
semester" — true, and useless, because it no longer says which building.



---

## 5. Your choice

<!-- YOU WRITE THIS ONE TOO.

     Pick something you actually care about getting right. It could be about
     speed, about refusals, about a particular kind of question your corpus
     handles badly, about source attribution being correct rather than merely
     present — anything, as long as it names a number or an observable
     outcome. -->

For all 5 of my test questions, the file named in the answer is one that
actually contains the fact stated — not just any file that came back from
retrieval.

**Why this target:**
Criterion 2 only asks that a source gets named, which a system can satisfy
while citing the wrong file. Retrieval hands the model 5 chunks and only one
usually holds the answer — my meal plan question returns
`admin_meal_plan_changes.txt` alongside four dining hall documents, and citing
any of those four would look right at a glance. That's the failure I'd rather
catch, so I'm checking the citation against the chunk, not just its presence.



---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 2 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath it, like this:

         ## 1. Retrieved chunks contain the answer

         For at least 4 of my 5 test questions, the retrieved chunks include
         one that contains the answer.

         **Why this target:** ...

         > **Revised in unit 2:** For at least 4 of 5 questions, the top three
         > results contain the answer.
         >
         > **Why revised:** I couldn't judge "the chunks include one that
         > contains the answer" the same way twice — I scored two questions
         > differently on Monday than on Wednesday. The new version is
         > something I can actually check.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "I said 4 of 5 but got 2 of 5, so 2 of 5 is more realistic."

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.

     The whole reason the originals stay visible is so someone can see what you
     said before you knew the answer.
     ───────────────────────────────────────────────────────────────────────── -->
