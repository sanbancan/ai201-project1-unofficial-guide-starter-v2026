# The Unofficial Guide

<!-- Replace this line with your name and which corpus you picked. -->

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

This system answers practical student-life questions using the `advice_threads`
corpus. It retrieves the most relevant student discussion, checks whether the
best match is close enough to the question, and refuses questions that are
outside the corpus. For supported questions, a language model writes a brief
answer using only the retrieved documents and names the source file.

## Chunking Strategy

**Chunk size:** 800 characters
**Overlap:** 0 characters

The advice-thread documents are already short, focused conversations. Their
lengths range from 317 to 793 characters, so keeping each thread together
preserves the question, replies, and context needed to answer it. I used a
paragraph-aware limit of 800 characters to avoid the starter's 2-character
tail chunk while still giving longer documents a safe paragraph boundary.

<!-- What about YOUR documents made you pick these numbers? Short posts and
     long sectioned guides don't want the same chunking, and "800 seemed
     reasonable" earns nothing. Point at something you noticed when you read
     the documents in Milestone 1.

     If you changed your mind partway through, say so and say why. That's worth
     more than pretending you got it right first time.

     Milestone 3. -->

## Sample Chunks

<!-- Five chunks, pasted as text. Label each one and name the file it came from
     AND the function that produced it — the grader checks your code against
     what you claim here.

     `python app.py chunks -n 5` prints all three for you. Copy them straight
     across.

     Milestone 3. -->

**Chunk 1** — source: `thread_bike_commute.txt#0` — produced by: `chunker.py::split_documents`

```
THREAD: Is a bike worth it for a 20 minute walk commute?

--- reply 1 (14 votes) ---
Yeah. Cuts an 18 minute walk to about 6. The thing nobody mentions is storage —
covered bike parking exists at three buildings and is full by 9am at all three.

--- reply 2 (9 votes) ---
Counterpoint, I sold mine. Between November and March the paths are either icy or
salted and salt destroys a drivetrain in one season.

--- reply 3 (22 votes) ---
Both true. I keep a cheap bike for September to November and walk the rest of the
year. Total cost was about $120 for the bike and I don't care what happens to it.

--- reply 4 (5 votes) ---
If you do get one, the campus does free registration and it's the only reason I
got mine back after it was taken.
```

**Chunk 2** — source: `thread_first_gen.txt#0` — produced by: `chunker.py::split_documents`

```
THREAD: Anything specific for first-generation students?

--- reply 1 (33 votes) ---
The advising office has a specific programme and it is genuinely good, but it is
opt-in and badly publicised. Ask for it by name.

--- reply 2 (41 votes) ---
The thing I'd say: the unwritten rules are the hard part, not the coursework. Ask
about the unwritten rules explicitly. People are happy to explain them and nobody
volunteers them.

--- reply 3 (16 votes) ---
Emergency fund for textbooks and travel exists and is not means-tested beyond a
short form.
```

**Chunk 3** — source: `thread_laptop_specs.txt#0` — produced by: `chunker.py::split_documents`

```
THREAD: How much laptop do I actually need for CS courses?

--- reply 1 (31 votes) ---
Less than the recommended spec page says. 16GB of RAM is the one number worth
paying for; everything else you'll never notice.

--- reply 2 (18 votes) ---
Adding: the lab machines exist and are better than anything you'll buy. For the
heavy assignments people just use those.

--- reply 3 (12 votes) ---
I did two years on an 8GB machine and it was fine until the last project, at which
point it very much wasn't. 16 is the answer.
```

**Chunk 4** — source: `thread_office_hours_etiquette.txt#0` — produced by: `chunker.py::split_documents`

```
THREAD: Is it weird to go to office hours with no specific question?

--- reply 1 (44 votes) ---
No, and this is the single most common thing first years get wrong. 'I'm following
the lectures but I don't feel like I understand the shape of it' is a completely
normal thing to say.

--- reply 2 (29 votes) ---
They're usually empty. You are doing the instructor a favour by turning up.

--- reply 3 (18 votes) ---
If it helps, treat it as a standing appointment. Go every week for a month and it
stops feeling like a thing.
```

**Chunk 5** — source: `thread_professor_email.txt#0` — produced by: `chunker.py::split_documents`

```
THREAD: Do professors actually answer email?

--- reply 1 (21 votes) ---
Varies enormously. General rule I've found: if the syllabus states a response
window, it's honoured. If it doesn't, assume 48 hours and don't panic before then.

--- reply 2 (33 votes) ---
Office hours are dramatically more effective than email for anything that takes
more than two sentences to answer. They're also usually empty.

--- reply 3 (15 votes) ---
Empty office hours is the biggest unused resource here and I say that having
wasted a year not going.
```

## Sample Answer

<!-- One complete question and answer, pasted as text, with the source line
     visible. Milestone 4. -->

**Question:** What laptop memory do students say is worth paying for in CS courses?

**Answer:** Students say that 16GB of RAM is worth paying for in CS courses.

**Source:** `thread_laptop_specs.txt`

```
Students say that 16GB of RAM is worth paying for in CS courses.

Source: thread_laptop_specs.txt
```

**My relevance cutoff:**

I set the cutoff to **0.65**. The five in-corpus questions had best distances
from 0.2090 to 0.4806, while the five out-of-scope questions ranged from
0.8280 to 0.9517. The gap between 0.4806 and 0.8280 means 0.65 accepts the
covered questions while refusing the unrelated ones. I kept `TOP_K = 5` because
the correct chunk was in the top result for all five covered questions, while
the remaining results provide useful context for grounded answers.

<!-- The number you set in config.py, and how you got there.

     You ran five questions your corpus covers and the five in OUT_OF_SCOPE
     that it clearly doesn't, and wrote down the best distance for each. What
     did those two groups look like? Where was the gap? Put the actual numbers
     here — the table below wants all ten rows.

     Milestone 4. -->

| Question | In corpus? | Best distance |
|---|---|---|
| What laptop memory do students say is worth paying for in CS courses? | Yes | 0.2090 |
| How long do students suggest waiting before worrying that a professor has not answered an email? | Yes | 0.4108 |
| What happens to the printing quota between semesters? | Yes | 0.3482 |
| What is the main storage-related problem with commuting by bike to campus? | Yes | 0.3732 |
| Is it acceptable to attend office hours without a specific question? | Yes | 0.4806 |
| What is the capital of Mongolia? | No | 0.9479 |
| How do I change the oil in a diesel engine? | No | 0.9299 |
| Who won the 1994 World Cup? | No | 0.9517 |
| What is the recommended dosage of ibuprofen for a headache? | No | 0.8280 |
| How do I write a for loop in Rust? | No | 0.8712 |

## How I Used AI

<!-- Two specific moments. For each: what you asked for, what came back, and
     what you changed about it.

     "I asked Claude to write the chunking function from my notes. It ignored
     the overlap, so I added that myself" is the level of detail we're after.
     "I used AI to help me code" is not.

     Milestone 5. -->

**1.**

I asked an AI assistant to inspect the starter chunker and help pressure-test
whether fixed 800-character windows fit the advice-thread corpus. It confirmed
that the starter produced a 2-character tail chunk, so I changed the strategy
to keep each short thread together at paragraph boundaries, with no overlap.

**2.**

I asked an AI assistant to help check the retrieval results and cutoff choice
for the five covered and five out-of-scope questions. The measured distances
showed a gap between 0.4806 and 0.8280, so I chose a 0.65 cutoff and tightened
the grounding instruction to require an exact refusal for unsupported answers
and a filename for supported answers.

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
| 1. Retrieved chunk contains the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Sampled chunks express complete thoughts | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Cited source contains the expected answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |

### Evidence from the before run

Source: `results/run_2026-09-23_1610_before.md`, produced by
`run_eval.py::main` and `run_eval.py::check_out_of_scope`.

**Criterion 1 — retrieved chunks contain the answer:**

The report shows the top result for each question contains the expected answer.
For example, the first question returned:

```
Students say that 16GB of RAM is worth paying for in CS courses.

Source: thread_laptop_specs.txt
```

The same report shows the other four answer-bearing top sources as
`thread_professor_email.txt`, `thread_printing.txt`, `thread_bike_commute.txt`,
and `thread_office_hours_etiquette.txt`. Therefore each run scored 5/5.

**Criterion 2 — every answer names a source:**

The report's answer output includes a source for every run. One example is:

```
The printing quota does not roll over between semesters (thread_printing.txt).
```

All 15 answers named a source document, so each run scored 5/5.

**Criterion 3 — the gate stops out-of-corpus questions:**

The report produced by `run_eval.py::check_out_of_scope` says:

```
Produced by `run_eval.py::check_out_of_scope`, cutoff 0.65. Refused 5 of 5.
```

Because retrieval is deterministic, that 5/5 result is recorded in all three
run columns.

**Criterion 4 — chunks express complete thoughts:**

The five samples printed by `app.py::cmd_chunks` were complete thread-level
chunks, with the title and replies kept together. The samples ranged from the
complete bike-commute discussion to the complete professor-email discussion;
none cut a sentence in half. This scored 5/5.

**Criterion 5 — cited source contains the expected answer:**

The report's answer for the laptop question says:

```
Students say that 16GB of RAM is the one number worth paying for in CS courses.

Source: thread_laptop_specs.txt
```

Checking the cited filename against each retrieved document showed the expected
phrase in the cited source for all five questions in all three runs: 5/5 each.

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
| 1 | Retrieved chunks contain the answer | MET | All three runs had the answer-bearing chunk for all 5 questions. |
| 2 | Every answer names a source | MET | All 15 generated answers named a source document. |
| 3 | Gate stops out-of-corpus questions | MET | The gate refused 5/5 out-of-scope questions. |
| 4 | Sampled chunks express complete thoughts | MET | All 5 sampled chunks were complete thread-level thoughts. |
| 5 | Cited source contains the expected answer | MET | Every cited file contained the expected answer phrase. |

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
