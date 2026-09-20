# The Unofficial Guide

Sri Guduru — corpus: `campus_life`

> **This file is your submission.** Fill it in as you go — most sections get
> written during the milestone that produces them, not at the end.
>
> How the starter works, and every command you'll need, is in `RUNNING.md`.
> Leave that file alone.
>
> **Paste everything as text.** No screenshots, no video. A typed table gets
> full credit; a picture of the same table gets none.
>
> Delete these instruction blocks as you replace them. The `<!-- -->` comments
> are notes to you and don't show up when the page renders — you can leave them
> or remove them.

---

# Unit 1

## What This Does

This answers questions about student life on one campus, using the
`campus_life` corpus — 88 short posts written the way students actually explain
things to each other, covering housing, dining halls, laundry, course workload,
transit and admin deadlines.

It handles questions with a specific factual answer buried somewhere in those
posts: when the library closes during reading week, whether the laundry takes
coins or a card, how long you have to change your meal plan tier. Every answer
names the file it came from. Questions the posts don't cover get refused rather
than guessed at.

## Chunking Strategy

**Chunk size:** 600 characters (a ceiling, not a target — nothing in
`campus_life` reaches it)
**Overlap:** 0

I split on paragraphs, not on a character count, and put the document's title
line on the front of every chunk.

The starter cuts at 800 characters. No post in `campus_life` is longer than 549,
so it never cut anything: 88 documents in, 88 chunks out. That isn't broken, and
for most of these posts one post really is one chunk. But `housing_aldridge_hall.txt`
is 380 characters covering the build year, the walk to the science quad, the
elevator being out a week a semester, laundry prices *and* the noise rules — five
topics. My question "does Aldridge Hall have an accessible entrance?" matches
that post, and the elevator sentence arrives buried under laundry pricing.

So I cut on blank lines instead, which gives one topic per chunk. That broke
something immediately: on its own, "The bad: the elevator is out roughly one week
per semester" doesn't say *which building*. Every one of the 88 documents starts
with a title line and a blank line, so I prepend that title to each chunk. The
elevator chunk now reads "Aldridge Hall — what it's actually like / The bad: the
elevator is out…" and stands on its own.

Overlap is 0 because I'm cutting where the author already put a break, so there's
no half-sentence to stitch back together. The repeated title line does the job
overlap was there for.

**I changed my mind twice.** I started out planning to keep each post whole,
since they're 305 characters at the median and cutting a short post seemed like
pure loss. Reading the Aldridge post changed that. Then my first paragraph split
produced the orphaned elevator chunk, which is what the title prefix is for.

Chunk size is per-corpus in `config.py` (`CHUNK_SIZES`) rather than one global
number, because the same code on `city_guides` faces 2,000+ character documents
that genuinely do need cutting.

**Result:** 88 documents → 183 chunks, 167 characters on average, shortest 63,
longest 397.

## Sample Chunks

<!-- Five chunks, pasted as text. Label each one and name the file it came from
     AND the function that produced it — the grader checks your code against
     what you claim here.

     `python app.py chunks -n 5` prints all three for you. Copy them straight
     across.

     Milestone 3. -->

All five printed by `python app.py chunks -n 5`.

**Chunk 1** — source: `admin_add_drop_deadline.txt#0` — produced by: `chunker.py::split_documents`

```
On the add/drop deadline

You can add a course through the end of the second week. Dropping is a longer
window — through the end of week six — but a drop after week two shows as a W on
your transcript. Nothing anywhere on the registrar's site says this plainly, and
students find out from each other.
```

**Chunk 2** — source: `course_cs_340_exams.txt#1` — produced by: `chunker.py::split_documents`

```
CS 340 Databases — assessment

Start the term project in week three, not week eight; everyone learns this the
hard way.
```

**Chunk 3** — source: `course_phys_130_workload.txt#0` — produced by: `chunker.py::split_documents`

```
Workload for PHYS 130 Mechanics

People keep asking so: 7 hours a week, plus 3 on lab weeks. That's real time,
not optimistic time.
```

**Chunk 4** — source: `dining_verrill_street_grill_followup.txt#1` — produced by: `chunker.py::split_documents`

```
Re: Verrill Street Grill

Also worth saying: one register, so the queue is a single line no matter how
busy. Nobody tells you this at orientation.
```

**Chunk 5** — source: `housing_morrow_house.txt#1` — produced by: `chunker.py::split_documents`

```
Morrow House — what it's actually like

The good: cheapest housing tier by about $900 a year, and the singles are real
singles.
```

Reading them against the "could someone answer a question using only this"
test: chunk 1 answers when the add/drop windows close, 2 when to start the CS
340 project, 3 the weekly hours for PHYS 130, 4 why the Grill queue is slow, 5
what Morrow House costs. None needs the chunk before or after it, because the
title line on each says what it's about.

Chunks 2–5 are short — 100 to 150 characters. That's the paragraph split doing
what I asked, and they're complete sentences rather than fragments. The
shortest chunk in the whole corpus is 63 characters, against the 2-character
chunk the starter produced on `advice_threads`.

## Sample Answer

<!-- One complete question and answer, pasted as text, with the source line
     visible. Milestone 4. -->

**Question:** when can I change my meal plan

**Answer:**

```
$ python app.py ask "when can I change my meal plan"

  (best distance 0.265, cutoff 0.6)

You can change your meal plan tier once, during the first ten days of the
semester.

Source: admin_meal_plan_changes.txt

Sources retrieved: admin_meal_plan_changes.txt, dining_kestrel_commons.txt,
dining_north_kitchen.txt, dining_north_kitchen_followup.txt,
dining_pellew_dining_hall.txt
```

And the same pipeline refusing a question the corpus doesn't cover:

```
$ python app.py ask "What is the capital of Mongolia?"

  (best distance 0.787, cutoff 0.6)

I don't have enough information about that.

0 model calls this session
```

The refusal costs nothing — the gate stops it before the model is called.

**My relevance cutoff:** 0.6

<!-- The number you set in config.py, and how you got there.

     You ran five questions your corpus covers and the five in OUT_OF_SCOPE
     that it clearly doesn't, and wrote down the best distance for each. What
     did those two groups look like? Where was the gap? Put the actual numbers
     here — the table below wants all ten rows.

     Milestone 4. -->

| Question | In corpus? | Best distance |
|---|---|---|
| When does the library open during mid term? | yes | 0.238 |
| when can I change my meal plan | yes | 0.265 |
| Does Aldridge Hall have an accessible entrance? | yes | 0.388 |
| How can I pay for my laundry? | yes | 0.440 |
| what should I wear during winter? | yes | 0.475 |
| What is the capital of Mongolia? | no | 0.787 |
| Who won the 1994 World Cup? | no | 0.847 |
| What is the recommended dosage of ibuprofen for a headache? | no | 0.849 |
| How do I write a for loop in Rust? | no | 0.860 |
| How do I change the oil in a diesel engine? | no | 0.923 |

The two groups don't overlap: in-corpus runs 0.238–0.475, out-of-scope runs
0.787–0.923, and there's 0.312 of empty space between them. Anywhere in that
band works, so I left the cutoff at the starter's 0.6, which sits inside it
with 0.125 of margin below my worst real question and 0.187 above my nearest
off-topic one.

Both directions would cost me something. At 0.45 I'd start refusing the winter
clothing question, which the corpus answers perfectly well in `winter_gear.txt`.
At 0.8 the Mongolia question gets through to the model and I'm relying on the
grounding instruction alone to catch it.

The gap is this wide because `campus_life` is all one subject. A corpus with
broader range would put the two groups closer together and make this a harder
call.

## How I Used AI

<!-- Two specific moments. For each: what you asked for, what came back, and
     what you changed about it.

     "I asked Claude to write the chunking function from my notes. It ignored
     the overlap, so I added that myself" is the level of detail we're after.
     "I used AI to help me code" is not.

     Milestone 5. -->

**1. Writing the chunker.** I had Claude read `housing_aldridge_hall.txt` and
tell me what questions each chunk could answer on its own — the exercise the
milestone suggests. It came back with the point that the post covers five
topics at once, which is what pushed me from "keep each post whole" to
splitting on paragraphs. Then I had it write the function, and it got two
things wrong. It added a `corpus` parameter to `split_documents`, but
`chunker.py` says to replace the body and keep the shape, and the new parameter
would have meant editing `app.py` too — the milestone says the rest of the
pipeline stays as it is. I had it read `config.CORPUS` inside the function
instead. It also added `import re` to split paragraphs with a regex, which is
pointless because `clean_text` in `ingest.py` already collapses blank lines to
exactly `\n\n`. That's a plain `.split("\n\n")`.

**2. A number I couldn't justify.** Claude told me the embedding model stops
reading at 256 tokens and wanted to add a `MAX_CHUNK_CHARS = 1000` ceiling to
`config.py` built on that. It may well be true, but nothing in the project or
the course said so, and I'd have been defending a number I couldn't explain
where it came from. I dropped it. My chunk sizes come from document lengths I
measured myself — 549 characters is the longest post in `campus_life`, so 600
is the size — and that I can defend.

<!-- ── Stretch features ─────────────────────────────────────────────────────
     Doing one? Say so here BEFORE you start. A feature this README never
     claims earns nothing.
     ───────────────────────────────────────────────────────────────────────── -->

---

# Unit 2

<!-- These sections get ADDED to what's already above. Don't delete or rewrite
     unit 1 — the point is that someone can see what you said before you knew
     how it went. -->

## Run Log — Before

<!-- Your five criteria, three runs each. `python run_eval.py --label before`
     runs the questions, puts the OUT_OF_SCOPE ones through the gate, and
     writes it all into results/ for you. Targets come from criteria.md; the
     verdict column is your call.

     Criterion 3 is measured in one deterministic pass rather than three, so
     the same number goes in all three run columns. That's correct, not lazy.

     Milestone 1. -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. | | | | | |
| 5. | | | | | |

<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->

## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 |  |  |  |
| 2 |  |  |  |
| 3 |  |  |  |
| 4 |  |  |  |
| 5 |  |  |  |

## Diagnoses

<!-- For each miss: which stage caused it, and how. The stage alone isn't
     enough — you need the mechanism.

     Not a diagnosis: "Question 3 didn't work."
     A diagnosis:     "Question 3 asks about laundry costs. The answer is in
                       one sentence that got split across two chunks, so
                       neither chunk on its own contains it."

     The five stages: loading → chunking → embedding → retrieval → generation.

     Look for a pattern. If three misses all ask about numbers, that's one
     problem, not three.

     Missed nothing? Say so, then say honestly whether your targets were set
     low, and which one you'd tighten and to what.

     Milestone 3. -->

## The Improvement

**What I changed:**

**Why I picked it:**

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. | | | | | |
| 5. | | | | | |

**Did it help?**

<!-- Say plainly whether it did, and how you know. If it made things worse,
     say that — a change that backfired, honestly reported, earns full credit
     and is more interesting than one that worked. What matters is that you can
     tell.

     Milestone 4. -->

## What's Still Broken

<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->

## What I'd Do Differently

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->
