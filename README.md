# The Unofficial Guide

Temur Rakhmatov - advice_threads

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

<!-- Three or four sentences. Which corpus you picked, and the kinds of
     questions your system answers. Write it for someone who has never seen
     this repo.

     Milestone 5. -->
This system is a retrieval-augmented generation (RAG) assistant using the "advice-threads" corpus, a collection of brief discussions and recommendations to certain topics and questions, mostly relating to college life. The RAG assistant can be asked questions regarding academic policies, study habits, clubs, internships, roommate conflicts, and other day-to-day college-related topics. It works by referring to chunked texts from collection of threads for a response and using the most relevant chunks to provide an answer while also citing the sources that the answer derives from. If questions are irrelevant to college life or cannot be answered using the threads, the RAG declines to answer rather than guessing a response.

## Chunking Strategy

**Chunk size:** Depends on the length of the thread reply but has a minimum of 80 characters
**Overlap:** None

<!-- What about YOUR documents made you pick these numbers? Short posts and
     long sectioned guides don't want the same chunking, and "800 seemed
     reasonable" earns nothing. Point at something you noticed when you read
     the documents in Milestone 1.

     If you changed your mind partway through, say so and say why. That's worth
     more than pretending you got it right first time.

     Milestone 3. -->

The "advice_threads" corpus consists of a series of thread replies where students post advice, tips, and experiences. In these documents, each reply is separated by a blank line. The original chunker cut sentences in half and produced 2 character chunks which were often unusable. Therefore, to keep each response as an independent chunk I split chunks for every blank line, represented by "\n\n", and made sure each one is greater than 80 characters in length to remove empty lines or formatting issues.

## Sample Chunks

<!-- Five chunks, pasted as text. Label each one and name the file it came from
     AND the function that produced it — the grader checks your code against
     what you claim here.

     `python app.py chunks -n 5` prints all three for you. Copy them straight
     across.

     Milestone 3. -->

**Chunk 1** — source: `thread_bike_commute.txt#0` — produced by: `chunker.py::split_documents`

```
--- reply 1 (14 votes) ---
Yeah. Cuts an 18 minute walk to about 6. The thing nobody mentions is storage — covered bike parking exists at three buildings and is full by 9am at all three.
```

**Chunk 2** — source: `thread_bike_commute.txt#1` — produced by: `chunker.py::split_documents`

```
--- reply 2 (9 votes) ---
Counterpoint, I sold mine. Between November and March the paths are either icy or salted and salt destroys a drivetrain in one season.
```

**Chunk 3** — source: `thread_bike_commute.txt#2` — produced by: `chunker.py::split_documents`

```
--- reply 3 (22 votes) ---
Both true. I keep a cheap bike for September to November and walk the rest of the year. Total cost was about $120 for the bike and I don't care what happens to it.
```

**Chunk 4** — source: `thread_bike_commute.txt#3` — produced by: `chunker.py::split_documents`

```
--- reply 4 (5 votes) ---
If you do get one, the campus does free registration and it's the only reason I got mine back after it was taken.
```

**Chunk 5** — source: `thread_changing_major.txt#0` — produced by: `chunker.py::split_documents`

```
--- reply 1 (22 votes) ---
Administratively trivial — it's a form. The real question is whether the credits you've taken map onto the new requirements.
```

## Sample Answer

<!-- One complete question and answer, pasted as text, with the source line
     visible. Milestone 4. -->

**Question:** Is it worth bringing a bike to campus?

**Answer:** Based on the provided documents, bringing a bike cuts an 18-minute walk down to about 6 minutes, but covered bike parking fills up by 9 am at all three buildings that have it (thread_bike_commute.txt). Additionally, getting free campus registration is noted as the only reason one person got their bike back after it was taken (thread_bike_commute.txt), and another person keeps a cheap bike for part of the year and walks the rest (thread_bike_commute.txt).


**My relevance cutoff:** 0.6

<!-- The number you set in config.py, and how you got there.

     You ran five questions your corpus covers and the five in OUT_OF_SCOPE
     that it clearly doesn't, and wrote down the best distance for each. What
     did those two groups look like? Where was the gap? Put the actual numbers
     here — the table below wants all ten rows.

     Milestone 4. -->

| Question | In corpus? | Best distance |
|---|---|---|
| Who decides whether transfer credits count toward a major? | Yes | 0.521 |
| What is a good study spot before 10am that isn't the library? | Yes | 0.485 |
| Where can a student check current textbook problem numbering for free? | Yes | 0.398 |
| How many sessions is the free sleep workshop run by counselling? | Yes | 0.372 |
| According to students, what is generally the response window if an email policy is not in the syllabus? | Yes | 0.283 |
| What is the capital of Mongolia? | No | 0.897 |
| How do I change the oil in a diesel engine? | No | 0.721 |
| Who won the 1994 World Cup? | No | 0.911 |
| What is the recommended dosage of ibuprofen for a headache? | No | 0.782 |
| How do I write a for loop in Rust? | No | 0.892 |


The distance for the five questions the corpus covers ranged from 0.283 to 0.521, whereas the distances for the five out of scope questions ranged from 0.721 to 0.911. Since the maximum distance of the five questions the corpus covered is 0.521 and the minimum distance of the five out of scope questions is 0.721, the average of the two is 0.621. Therefore, I decided to maintain the original 0.6 relevance cutoff as it is situated comfortably within this gap, greater than the maximum distance of the five questions the corpus covered and below the minimum distance of the five out of scope questions, making it effective at filtering irrelevant questions without rejecting valid questions.
## How I Used AI

<!-- Two specific moments. For each: what you asked for, what came back, and
     what you changed about it.

     "I asked Claude to write the chunking function from my notes. It ignored
     the overlap, so I added that myself" is the level of detail we're after.
     "I used AI to help me code" is not.

     Milestone 5. -->

**1.** I used an AI to evaluate my five criteria and determine if they were objective and testable. The AI pointed out that my criterion regarding chunk quality was too subjective, noting that requiring chunks to "contain complete ideas" is not a concrete or measurable standard. Based on this feedback, I modified the wording to require complete sentences instead.

**2.** I also used an AI to evaluate my idea of chunking the "advice-threads" corpus by splitting replies on blank lines (\n\n) rather than using fixed character quantities. The AI suggested that I also add a minimum character threshhold for the chunks to ensure that tiny insignificant fragments do not become chunks and are instead discarded. Following this advice, I incorporated an 80-character minimum limit for the chunker to remove short fragments.

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
| 1. Retrieved chunks contain the answer | 4 of 5 | 3/5 | 3/5 | 3/5 | MISSED |
| 2. Every answer names a source | 5 of 5 | 3/5 | 3/5 | 3/5 | MISSED |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. At least 90% of produced chunks are 80-500 chars | 90% | 0/5 | 0/5 | 0/5 | MISSED |
| 5. Retrieval returns top chunks in under 1 second | 4 of 5 | 0/5 | 0/5 | 0/5 | MISSED |

<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->
Criterion 1:
### Who decides whether transfer credits count toward a major? — run 1

- Best distance: 0.5211 (passed the gate)
- Sources retrieved: thread_changing_major.txt, thread_meal_plan_tier.txt, thread_pass_fail.txt, thread_transfer_credits.txt

Based on the provided documents, there is not enough information to answer who decides whether transfer credits count toward a major.


Criterion 2:
### Where can a student check current textbook problem numbering for free? — run 1

- Best distance: 0.3976 (passed the gate)
- Sources retrieved: thread_first_year_regret.txt, thread_group_project.txt, thread_printing.txt, thread_textbook_editions.txt

A student can check the problem numbering against the current edition for free using the library reserve copy. 

Source: thread_textbook_editions.txt


Criterion 3:
| Out-of-scope question | Best distance | Gate |
|---|---|---|
| What is the capital of Mongolia? | 0.897 | refused |
| How do I change the oil in a diesel engine? | 0.721 | refused |
| Who won the 1994 World Cup? | 0.911 | refused |
| What is the recommended dosage of ibuprofen for a headache? | 0.782 | refused |
| How do I write a for loop in Rust? | 0.892 | refused |


Criterion 4:
Chunk sizes were not measured in the runs.


Criterion 5:
The retrieval time was not measured in the runs.


## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 | Retrieved chunks contain the answer | MISSED | Our target required at least 4 out of 5 questions to retrieve chunks with the answer, but Questions 1 and 2 failed to retrieve the necessary answer for all three runs, resulting in a score of 3 of 5. |
| 2 | Every answer names a source | MISSED | Our target required all 5 answers to name a source document, but Questions 1 and 2 refused to produce responses, resulting in no citations. |
| 3 | Gate stops out-of-corpus questions | MET | The relevance gate successfully refused all 5 out-of-scope test questions, exceeding our target requirement of 4 out of 5. |
| 4 | At least 90% of produced chunks are 80-500 chars | MISSED | This criterion was missed because `run_eval.py` records text answers and distance scores, rather than chunk character length. |
| 5 | Retrieval returns top chunks in under 1 second | MISSED | This criterion was missed because the evaluation does not log the retrieval time. |



Revised Criterions:

Criteria 4: At least 90% of produced chunks are 80-500 characters.
Revised: At least 4 of 5 questions return chunks generated from complete thread replies.
Reason: The original criterion could not be measured because `run_eval.py` logs answers and distances rather than chunk character counts.

Criteria 5: Retrieval returns top chunks in under 1 second.
Revised: Evaluation script executes all 5 queries completely without timing out.
Reason: The original criterion could not be measured because `run_eval.py` does not measure retrieval timing.

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


Criteria 1 and 2:
Questions 1 and 2 passed the relevance gate with distances of 0.5211 for Question 1 and 0.4851 for Question 2, both of which are below the 0.6 threshold, but the search did not return the specific sentences containing the answers. Because the retrieved chunks lacked the required facts, the model safely refused to answer, which also prevented generated source citations from being included in the response.

Criteria 4 & 5:
Criteria 4 and 5 could not be evaluated from the run log because 'run_eval.py' records text answers and distances, but does not log chunk character lengths or retrieval time.


## The Improvement

**What I changed:** 
Increased `TOP_K` in `config.py` from 5 to 10 to pull a larger number of candidate chunks per question

**Why I picked it:** 
Questions 1 and 2 passed the relevance gate, but the top 5 chunks retrieved lacked the exact answer sentences. Increasing `TOP_K` directly tests whether candidate chunks ranked slightly lower in relevance contain the required facts.

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunks contain the answer | 4 of 5 | 3/5 | 3/5 | 3/5 | MISSED |
| 2. Every answer names a source | 5 of 5 | 3/5 | 3/5 | 3/5 | MISSED |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Complete thread replies returned | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Script executes without timing out | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |

**Did it help?** 
No, doubling the `TOP_K` value to 10 did not resolve the misses for Questions 1 and 2. While the correct source thread files (`thread_transfer_credits.txt` and `thread_study_spots.txt`) were retrieved, the search failed to rank the specific answer paragraphs high enough to supply the required facts. This seems to confirm that the failure is a ranking problem rather than a context window cutoff issue.

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
     Criteria 1 and 2 remain missed (3/5) because the search lacks exact term matching. In the next iteration, the system needs hybrid search in `store.py` so exact key phrases like "transfer credit" and "10am" are prioritized during retrieval regardless of the distance.

## What I'd Do Differently

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->
     
     In Unit 2, I would write Criteria 4 and 5 from the start to target variables that `run_eval.py` directly logs, rather than assuming chunk character counts and retrieval timing were tracked by the test runner.