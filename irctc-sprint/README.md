This assignment is essentially a **UX research and product discovery exercise**, not a coding assignment.

Here's what you need to do.

## 1. Create the GitHub repository

Create a public repository named:

```text
irctc-sprint
```

Clone it and create the branch:

```bash
git clone https://github.com/<your-username>/irctc-sprint.git
cd irctc-sprint
git checkout -b irctc-sprint
```

---

## 2. Create the required folders

```bash
mkdir -p part-a part-b assets/screenshots

touch part-a/PROBLEMS.md
touch part-b/SPECS.md
touch part-b/AI-FEATURE.md
touch part-b/MATRIX.md
```

Commit.

---

## 3. Complete `part-a/PROBLEMS.md`

This is your main task.

You need to document **6 problems**.

### 3 are already given

You need to investigate:

* Tatkal booking crash
* Search filters
* Seat selection reset

Do **not** just copy the prompt.

Actually:

* Open IRCTC
* Reproduce as much of the flow as possible
* Search Reddit/X/App Store reviews for evidence
* Document the issue

---

### Find 3 more yourself

Spend about 20 minutes exploring:

* Train search
* Booking flow
* PNR status
* Cancellation
* TDR
* Mobile browser
* Accessibility

Write down every annoying thing you notice.

Choose the best 3.

Examples (don't use these unless you actually verify them):

* Poor mobile responsiveness
* Confusing quota selection
* Difficult cancellation flow
* PNR page lacks explanations
* Hidden refund information
* Session timeout without warning

---

## 4. For every problem include

```
Problem title

What is broken

Who is affected

Frequency

Current flow
Step 1
Step 2
...
Step 6+

Where it breaks

(For self-discovered)
How I found it

Screenshot
```

---

## 5. Take screenshots

Whenever you notice something:

```
Windows

Win + Shift + S
```

Save inside

```
assets/screenshots/
```

---

## 6. Commit

After the first three.

After the next three.

Finally push everything.

---

## 7. Open a Pull Request

Branch

```
irctc-sprint
```

→

```
main
```

Title

```
Part A — IRCTC Problem Discovery: 6 Pain Points Documented
```

The PR description should include:

* One screenshot
* Table of all six issues
* One real user complaint (with source)
* Confirmation that your three self-discovered issues are different

---

## 8. Record a 3–5 minute video

Show:

* Yourself on camera
* Browser open
* One self-discovered issue
* Walk through it
* Explain:

  * what breaks
  * who is affected
  * how often
  * how you'd fix it in Part B

Upload to Google Drive with public viewing enabled.

---

## Deliverables

By the end, you should have:

```
irctc-sprint/
│
├── README.md
├── part-a/
│   └── PROBLEMS.md      ← Main deliverable
├── part-b/
│   ├── SPECS.md
│   ├── AI-FEATURE.md
│   └── MATRIX.md
└── assets/
    └── screenshots/
```

There is **no coding required** in Part A. The quality of your observations, documentation, screenshots, and reasoning is what will be evaluated.
