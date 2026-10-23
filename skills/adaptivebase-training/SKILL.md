---
name: adaptivebase-training
description: Use when a connected AdaptiveBase athlete or coach wants to begin training, establish baselines, log completed sets, review progress, check daily sleep and recovery, identify gym equipment, adapt a program, or follow Performance programs, review Base Points or new first-party flows, or propose training for a consenting client. Do not use for unrelated questions or generic fitness advice without an AdaptiveBase request.
---

# AdaptiveBase training

AdaptiveBase stores authoritative facts; the AI supplies conversation and reasoning.
Retrieve current records instead of relying on conversation memory. Only a successful
result proves a change. Never claim an attempted or failed write was saved.

## Connect and discover

Discover the 19 plugin tools and exact schemas. Use the names in the bundled
[tool contracts](references/tools.md); the plugin endpoint does not use the native
AgentCore prefix. Grouped tools take `action`, `input`, and, for writes, a unique
UUID `idempotencyKey`. Live discovery wins over bundled contracts.

Use `get_profile` to identify the account and `get_training_status` to check setup.
If `requiresSetup`, direct the user to the returned `setupUrl`. Early access approval,
sign-in, setup, and connector entitlement are distinct. Never request passwords,
codes, tokens, payment details or keys in chat. AI-provider plans are separate.

## Baseline and daily check-in

1. `get_training_status` returns `context`, `baselines`, or a setup link. Missing
   programs or measurements mean unknown, not zero or a saved recommendation.
2. Ask goals, experience, schedule, session length, equipment, units/timezone, pain
   and restrictions. `manage_training_plan` action `profile` saves only supported
   profile fields; do not invent goal or injury fields outside the schema.
3. Agree conservative, submaximal baseline assessments. Use `record_readiness`
   action `assessment` with reported results, protocol and explicit units. Skipping
   is valid. Never fabricate clearance or require painful or maximal efforts.
4. Explain the starting plan and uncertainty. Only after agreement use
   `manage_training_plan` action `update` with current expectedVersion and reason.

At the first training interaction of each local day, inspect `context.dailyCheckIn`.
For missing health, offer accessible measurements or reported sleep. Use
`record_readiness` action `health` with local wake date, timezone, source and stable
source record ID; include only known measurements. This does not read Apple Health.
Keep HRV SDNN and RMSSD distinct; never sum competing sources. For missing recovery,
ask subjective sleep quality, energy, motivation, soreness and pain, and where the
athlete is willing fatigue, stress, illness symptoms and anything that may have
affected the night (alcohol, travel, a late meal); action `recovery` requires actual
reported facts. Honor declines, never block workouts or repeatedly ask for today's
recorded data. Measurements provide context, not a diagnosis.

`context.readinessAssessment` compares today with the athlete's own baselines (it
says when there is not yet enough data). Present it as a suggestion the athlete
decides on (keep easy, shorten or a recovery day), in wellness language; never
turn it into a new distance or pace, an injury or illness claim, or a reason to do
more.

## Work out and record

1. Read `get_today_workout`, status, current gym and recent exercise performance.
   Review pain/recovery and confirm the intended workout.
2. Resume an existing active session using `get_workout`; otherwise use
   `record_workout` action `start` to snapshot the agreed prescription.
3. Action `set` records completed work only. Resolve canonical exercise/session/set
   IDs from retrieved records. Clarify load units, reps and required RIR; RPE alone
   does not supply every required fact. API loads use pounds; convert kilograms
   explicitly. Distinguish prescribed and performed work.
4. Sets are immutable: there is no update_set. Explain correction limitations;
   do not duplicate a set or claim it was edited.
5. Action `exercise_complete` needs athlete confirmation. Action `complete` needs
   confirmation that the session is finished and reported session RPE. Never
   invent session RPE. Actions `run` and `mobility` record their required facts.

Reuse the same idempotencyKey and identical arguments on retries of one logical
write. Never add userId or infer access to another account. Reload on version
conflict and ask again if the proposal changes. Do not diagnose pain or prescribe
treatment; stop painful activity and direct concerning symptoms to professional care.

## Equipment, programs and coaches

Read [equipment and travel](references/equipment.md) for private photos and gyms.
Use published exercise IDs from `browse_exercises`, confirmed inventory, known load
limits and reported constraints. Rankings are not medical clearance or guarantees.

Read adaptationPolicy: FIXED means portal edits; GUIDED means
`manage_training_plan` action `propose` and athlete portal approval; FLEXIBLE permits
only enabled bounded changes. Do not loosen controls or replace a plan to evade them.
A saved template or proposed program is not an active program.

Read [coach workflows](references/coaching.md) for client work. Server-side consent
is required even for administrators. `get_progress` supplies deterministic analytics,
records and trends; `get_exercise_history` supplies actual sets. Use returned metrics,
not invented percentages or graphs. Estimated 1RM is not a measured maximum.

## Performance, guided strength and Your Base

Use `get_training_features` to read the Performance catalog, immutable full program
version, enrollments, definitions and method-grouped result history. Read the
prerequisites, safety guidance and dosing before discussing enrollment. Ask the
athlete to acknowledge every prerequisite and safety guidance, the pinned version,
start date and preferred weekdays. Only then use `manage_performance` action
`enroll` with the returned prerequisite IDs and `acknowledgeSafety:true`. These
are self-attestations, never medical clearance.

Record a Performance session as complete only after the athlete reports it. Read
the current enrollment version first; ask for RPE if they want to include it. Link
a native session only if a retrieved completed session actually exists. Withdrawal
also needs explicit agreement and the current version. Record assessment results
with reported values, units, date and actual timing method. Compare stopwatch,
timing-gate and video attempts only within their returned groups. Manual entries
never become verified wearable evidence.

For guided strength, discover the canonical exercise and actual equipment/load
increments, then use `assess_strength` start/attempt/skip/proposals. Ask every
attempt's repetitions, reserve, pain and form concerns; stop for pain or form
breakdown. Carry the returned state without changing its policy or inventing
attempts. Draft results are self-reported estimates, not saved baselines, programs
or workouts. Review and explicit agreement are required before any separate save.

Base Points policy describes levels and caps. Earned state/history require the
athlete's existing AI sharing preference; when sharing is absent, use the returned
Connected devices and Your Base links. Never change privacy settings to obtain data.

## First-party flows

Use `open_training_flow` for adaptive Program recommendations, strength baseline
review, Performance, achievements, running routes, Friends, Community, connected
devices/AI sharing, equipment, profile, coaching consent or account setup. Return
the exact URL and describe the action the athlete must take there. A link performs
no action. Do not claim to apply an adaptive recommendation, pair a watch, record
GPS, verify a phone number, send a friend request, grant consent, purchase a
membership or expose private social/contact data through these tools. Full-evidence
first-party decision engines are not exposed through this connector.
