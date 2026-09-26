# Training desk

We are building a **training tool** for people who run transit, security, and other kinds of disptch operations at three places: **UBC**, **Waterfront**, and **Park Royal**.

The phone-tower data we're given is a recording of those places (late 2025 through summer 2026). Trainees can practice on that recording: they watch a stretch of time, decide what they would have done, and get scored.

I don't think it would be appropriate to run some sort of simulation that shows the trainee the effect of sending a dispatch/taking a certain action. I say this because (i) I can't think of a datasource of dispatch actions in Vancouver that we could use to train a model on to say "this dispatch resulted in this outcome", and (ii) this is synthetic data, so even if such a data source existed, we can't be sure that the synthetic data actually reflects it.   
  
As such I think we should score whether the trainee **noticed the abnormal moments** and **picked a sensible type of response**.

The main reason I think this is a good idea is because it's highly interactive, and has lots of room to expand on it (e.g. see the last section)

This document is a starting point for the team, not a full spec. Please bring your creativity, and add ideas!

---

## The two parts we need no matter what

### 1. Find the **abnormal** moments in Databricks

Someone has to go through the provided data (and any extra open data we add) and **collect the moments a trainee should have handled**.

That list is the answer key.

From the files we already have, we can look for things like:

- People lingering a long time
- A place that is usually fine at that hour, but this time is not
- People still at an area after the bus/train of the night 
- Quiet stretches 
- ... and anything else you can think of

Extra data we might want to surface more insights:

- Weather (rain vs dry can mean “shelter” vs “wait for a train”)
- TransLink schedules (when the last train/bus was)
- data about big days/events(FIFA, Olympics, Boxing Day, exams at UBC, Canada Day, a cruise Saturday)
- City 311-style complaints
- news data
- ... and anything else you can think of

Once we find abnormalities, we need them to be structured in a table/some sort of format that we can use as part of the app frontend.

This work is also what the hackathon means by “analysis in Databricks” on the rubric. Do it in a documented way (notebooks, or a pipeline, etc.).

I would encourage using AI in this phase, to help you get around the learning curve for Databricks. You can use Genie or hook up the Databricks MCPs to your coding agent.

---

### 2. The training app

There are three main parts to this:

**Replay**  
A screen where time moves through the recording. The trainee can see how busy each site was (maybe a dot plot or something) and how long people were staying. We could also drop hints during (e.g. “nights like this usually stay bad until 7pm,” “last trip has left,” “it’s raining”).

**Actions**  
The trainee can do something: open a ticket, ignore it, send people, treat it as extra transit, treat it as indoor holding / shelter, mark it as a false alarm, wait and see, etc. etc.. We should save the actions taken so we can grade them later**.**

**Scoring**  
After the run, compare what they did to the analysis we did in part 1.

- Did they catch the abnormal moments?
- Did they use a response that matches what we said was reasonable?
- and other criteria we can think of

We can put that on a scorecard, and also maybe add a short write-up: what they caught, what they missed, and why a choice was a good/poor fit.

**Technical suggestion for the scoring functionality:**  
We do **not** need an LLM to be the grader. A practical approach could be:

- Each scenario has a listed good answer (or a short list of acceptable answers), which we write after mining the data.
- Anything else gets a **stored explanation** of why it is the wrong (or weaker) answer - e.g. “last trip had already left, so extra trains are not the point; someone on site is.”
- The score is then mostly counting matches. The write-up can be those explanations plus a few sentences of commentary.

This is not a hard and fast suggestion so if you can think of something better, go for it.

---

---

## Split of work (rough)


| Area       | Job                                                                                                |
| ---------- | -------------------------------------------------------------------------------------------------- |
| Databricks | Clean the three files, find abnormal moments, optionally join extra data, export the scenario list |
| App        | Replay the timeline, let the trainee act, save those actions                                       |
| Scoring    | Match actions to the scenario list, show a scorecard and a short report                            |


---

## Stretch goal: a background miner

After the core parts work, we could run something **in the background** that keeps looking at the data (and any extra sources we added) for **new odd moments or interesting patterns**.

The idea: the training set does not have to be a one-time list we write. A background agent can keep analyzing, flag candidates, and propose them as new scenarios for the app.

I think this is cool and will help us maybe score extra points on the rubric, since Databricks will actually be a live part of our system rather than a one time analysis tool.