---

name: diagnosing-bugs
description: Use when diagnosing the cause of a bug, broken behavior, failing test, flaky issue, wrong output, runtime error, slow performance, or regression. Do not use for ordinary feature work or simple refactoring without a reported failure.
-----------------------------------------------------------------------------------------------------------------------------

# Diagnosing Bugs

Use this skill when debugging hard bugs or performance regressions.

Core rule: **build a feedback loop before guessing.**
A fix is not trusted until the original bug can be reproduced, fixed, and verified.

Before touching code, read `CONTEXT.md` if it exists. Also check nearby README files, ADRs, and test conventions.

---

## Phase 1 — Build a feedback loop

Create one command that can catch the exact bug.

Good feedback loops include:

1. failing test
2. curl / HTTP script
3. CLI command with fixture input
4. Playwright / Puppeteer script
5. replayed request, payload, trace, or event log
6. small throwaway harness
7. fuzz / repeated loop for flaky bugs
8. git bisect or differential test

The loop should be:

* **Red-capable** — catches the user’s exact symptom
* **Specific** — not just “something failed”
* **Fast** — ideally seconds
* **Repeatable** — deterministic, or high repro rate for flaky bugs
* **Agent-runnable** — one command, no manual steps if possible

If no loop can be built, say so clearly. List what was tried and ask for logs, HAR, trace, screen recording, fixture data, or access to the reproducing environment.

Do not make a confident root-cause claim without a red-capable loop.

---

## Phase 2 — Reproduce and minimise

Run the loop and confirm the failure matches the user’s report.

Capture the exact symptom:

* error message
* wrong output
* failing assertion
* slow timing
* screenshot
* log or trace

Then minimise the repro.

Remove inputs, config, services, steps, and data one at a time. Re-run after each change.

Done when every remaining part is necessary for the bug to appear.

---

## Phase 3 — Hypothesise

Create 3–5 ranked hypotheses before fixing.

Each hypothesis must be falsifiable:

> If `<cause>` is true, then `<test / observation>` should happen.

Avoid vague guesses.

When helpful, share the ranked list with the user, but do not block if the user is not available.

---

## Phase 4 — Instrument

Test one hypothesis at a time.

Prefer:

1. debugger / REPL / inspector
2. targeted logs
3. profiler, trace, flamegraph, or query plan for performance issues
4. temporary assertions near the suspected failure

Do not “log everything”.

Temporary debug logs must use a unique prefix, for example:

```txt
[DEBUG-a4f2]
```

Before finishing, grep and remove all debug logs.

Never log secrets, tokens, passwords, private data, or sensitive payloads.

---

## Phase 5 — Fix and regression test

If there is a correct test seam:

1. convert the minimised repro into a failing regression test
2. watch it fail
3. apply the smallest safe fix
4. watch it pass
5. re-run the original feedback loop
6. run nearby related tests

A correct test seam must exercise the real bug path.
Do not add a shallow test that gives false confidence.

If no correct seam exists, document that clearly.

For performance regressions, verify with numbers:

* before timing
* after timing
* sample size
* profiler or query evidence when useful
* clear threshold for “fixed”

---

## Phase 6 — Cleanup and Thai report

Before declaring done:

* [ ] original repro no longer fails
* [ ] regression test passes, or missing test seam is documented
* [ ] related tests pass
* [ ] debug logs are removed
* [ ] throwaway scripts are deleted or clearly marked
* [ ] sensitive artifacts are not committed

Final response/report must be written in **Thai** and include:

1. **อาการที่พบ** — bug คืออะไร
2. **วิธี reproduce** — ใช้คำสั่งหรือขั้นตอนอะไร
3. **สาเหตุหลัก** — root cause คืออะไร
4. **สิ่งที่แก้ไข** — แก้ตรงไหนและทำไม
5. **วิธีตรวจสอบผล** — ทดสอบอะไรแล้วผ่าน
6. **ความเสี่ยงที่เหลือ** — ถ้ามี
7. **ข้อเสนอป้องกันซ้ำ** — เช่น เพิ่ม test, log, validation, monitoring, หรือปรับ architecture

If the bug reveals weak architecture, recommend handing off to an architecture-improvement skill after the fix is verified.
