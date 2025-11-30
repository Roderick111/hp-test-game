# hp-test-game
# Auror Academy: Case Files
## Game Design Document v1.0

---

# Part I: Core Concept

## The Vision

Auror Academy: Case Files is a web-based investigative game where players train as magical law enforcement officers, learning to solve crimes while fighting their own cognitive biases. The game teaches applied rationality techniques through immersive detective work—players don't just learn *about* clear thinking, they practice it under pressure, see consequences of biased reasoning, and build genuine skills transferable to real life.

The core insight driving the design: **detective work is rationality made visceral**. When you're investigating a crime, the costs of confirmation bias are obvious (you catch the wrong person), the value of calibrated confidence is clear (you need to know how sure you really are), and the importance of considering alternative hypotheses is urgent (the real criminal escapes while you chase the wrong lead).

## Learning Goals

By the end of the full game, players will have practiced:

1. **Hypothesis Generation** — Generating multiple explanations before fixating on one
2. **Evidence Gathering Without Confirmation Bias** — Seeking evidence that distinguishes hypotheses, not just confirms favorites
3. **Calibrated Confidence** — Assigning probability estimates that match actual accuracy
4. **Pre-Mortem Planning (Murphyjitsu)** — Identifying failure modes before they happen
5. **Finding Cruxes** — Identifying the key factual questions that would resolve disagreements
6. **Noticing Bias in Real-Time** — Catching yourself when cognitive biases are influencing your thinking

## Core Design Principles

### Principle 1: Show, Don't Lecture

The game never delivers rationality lessons as text dumps. Instead, players discover principles through experience. If a player pursues a biased investigation strategy, they see it fail. If they make overconfident predictions, they see their calibration scores suffer. The game creates situations where clear thinking *obviously matters*, then lets players figure out what works.

### Principle 2: Failure is Formative

Getting things wrong is part of the learning process. Cases are designed so that common cognitive mistakes lead to plausible-but-incorrect conclusions. When players fail, they receive a detailed "Case Review" showing exactly where their reasoning went astray, which bias affected them, and what they could have done differently. Failure doesn't feel punishing—it feels like useful feedback.

### Principle 3: Transfer to Real Life

Every in-game technique maps directly to a real-world rationality skill. The game uses magical framing to make concepts memorable and engaging, but regularly surfaces the real-world application. A player who learns to avoid "Confirmation Charm" in investigations should recognize the same pattern when evaluating job candidates or reading news.

### Principle 4: Meaningful Choices

Players never make choices that are obviously "the good one" vs "the bad one." Biased choices are always *tempting*—they feel efficient, they match intuition, they're what the player's gut says to do. The game validates that these instincts aren't stupid; they're just sometimes wrong in predictable ways.

### Principle 5: Progressive Complexity

Early cases isolate single skills with clear feedback. Later cases combine multiple skills, introduce complications, and feature more subtle biases. A player who breezes through Mission 1 shouldn't breezes through Mission 10.

---

# Part II: Core Mechanics

## The Investigation Loop

Each case follows a five-phase structure. Every phase teaches specific rationality skills:

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│   BRIEFING ──► HYPOTHESIS ──► INVESTIGATION ──► PREDICTION     │
│      │          FORMATION       PHASE            PHASE         │
│      │              │              │                │          │
│      │              │              │                │          │
│      │              ▼              ▼                ▼          │
│      │         Generate       Gather evidence   Assign         │
│      │         multiple       strategically     calibrated     │
│      │         theories                         probabilities  │
│      │                                                         │
│      │                              │                          │
│      │                              ▼                          │
│      │                        RESOLUTION                       │
│      │                         PHASE                           │
│      │                           │                             │
│      │                           ▼                             │
│      │                    Confront suspect,                    │
│      │                    reveal truth,                        │
│      │                    receive feedback                     │
│      │                           │                             │
│      └───────────────────────────┘                             │
│                              │                                 │
│                              ▼                                 │
│                      CASE REVIEW                               │
│                 (learning consolidation)                       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Phase 1: Briefing

**What happens:** Player receives initial information about the case—a magical incident report, witness statement, or crime scene summary. Information is always incomplete and sometimes misleading.

**Player actions:** Read and absorb information. No choices yet, but the player is already being primed—the briefing is written to trigger specific cognitive biases (availability, anchoring, representativeness) that will be tested later.

**UI elements:**
- Official Ministry of Magic case file aesthetic
- "Classified" stamps, moving photographs, parchment textures
- Key facts highlighted but player can review full document

**Duration:** 1-2 minutes of reading

---

## Phase 2: Hypothesis Formation

**What happens:** Before investigating, player must generate possible explanations for what happened. This is crucial—it happens *before* gathering evidence to prevent premature fixation.

**Player actions:**
1. Generate at least 3 distinct hypotheses about what happened
2. Assign initial probability estimates to each (must sum to 100%)
3. For each hypothesis, identify what evidence would support or refute it

**The Hypothesis Board:**
```
┌─────────────────────────────────────────────────────────────────┐
│  HYPOTHESIS BOARD                              Case #AA-2847   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Theory A: [Player-generated or selected]           [40%] ▼    │
│  ├─ If true, I'd expect to find: _______________               │
│  └─ If true, I would NOT expect: _______________               │
│                                                                 │
│  Theory B: [Player-generated or selected]           [35%] ▼    │
│  ├─ If true, I'd expect to find: _______________               │
│  └─ If true, I would NOT expect: _______________               │
│                                                                 │
│  Theory C: [Player-generated or selected]           [20%] ▼    │
│  ├─ If true, I'd expect to find: _______________               │
│  └─ If true, I would NOT expect: _______________               │
│                                                                 │
│  Theory D: Something else I haven't thought of      [ 5%] ▼    │
│  (Always present - epistemic humility reminder)                │
│                                                                 │
│                          [Proceed to Investigation ►]          │
└─────────────────────────────────────────────────────────────────┘
```

**LLM Integration Opportunity (v2):**
In a more advanced version, players could type free-form hypotheses, and an LLM could evaluate whether they're genuinely distinct theories or just rewordings of the same idea. For v1, we provide a menu of pre-written hypotheses to select from, plus a "something else" option.

**Scoring:**
- Bonus points if the true answer was among player's initial hypotheses
- Bonus points for reasonable initial probability on the true answer
- Tracks "hypothesis diversity"—did player consider meaningfully different possibilities?

---

## Phase 3: Investigation

**What happens:** The heart of the game. Player gathers evidence by choosing where to look, who to interview, and what to examine. They have limited "Investigation Points" (representing time and resources), forcing prioritization.

**Player actions:**
1. Choose investigation actions from available options
2. Spend Investigation Points on chosen actions
3. Review evidence gathered
4. Update hypothesis probabilities based on evidence

**The Investigation Interface:**

```
┌─────────────────────────────────────────────────────────────────┐
│  INVESTIGATION                                    IP: ●●●●○○○  │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  LOCATIONS           WITNESSES          RECORDS                │
│  ┌──────────┐       ┌──────────┐       ┌──────────┐            │
│  │ Crime    │       │ Victim's │       │ Ministry │            │
│  │ Scene    │       │ Colleague│       │ Archives │            │
│  │   2 IP   │       │   2 IP   │       │   1 IP   │            │
│  └──────────┘       └──────────┘       └──────────┘            │
│  ┌──────────┐       ┌──────────┐       ┌──────────┐            │
│  │ Suspect's│       │ Neighbor │       │ Gringotts│            │
│  │ Home     │       │ Who Saw  │       │ Records  │            │
│  │   2 IP   │       │ Something│       │   2 IP   │            │
│  └──────────┘       │   1 IP   │       └──────────┘            │
│                     └──────────┘                               │
│                                                                 │
│  [Evidence Collected]        [Hypothesis Board - Update ►]     │
│  • Wand analysis report                                        │
│  • Initial witness statement                                   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

**The Critical Mechanic: Confirmation Bias Tracking**

The game silently tracks which hypotheses each investigation action is most relevant to. After the case, players see a visualization:

```
Your Investigation Pattern:
───────────────────────────────────────────────────
Theory A (your favorite): ████████████████  80%
Theory B:                 ███                15%  
Theory C:                 █                   5%

⚠️ CONFIRMATION BIAS DETECTED
You spent 80% of your investigation resources pursuing 
evidence related to your leading theory. This is natural
but dangerous—if Theory A was wrong, you wouldn't have
found out until it was too late.

BETTER STRATEGY: Prioritize evidence that DISTINGUISHES
between theories. Ask: "What would I find if A is true
but B is false? What if B is true but A is false?"
```

**Evidence Types:**
- **Confirming evidence** — Supports one hypothesis
- **Disconfirming evidence** — Rules out one hypothesis  
- **Discriminating evidence** — Distinguishes between hypotheses (most valuable!)
- **Misleading evidence** — Seems relevant but actually isn't
- **Red herrings** — Deliberately planted false leads

**LLM Integration Opportunity (v2):**
Witness interviews could be free-form conversations with LLM-powered characters. Players type questions; witnesses respond naturally (with their own biases, incomplete knowledge, and potential deception). This would dramatically increase immersion and allow for more sophisticated interrogation skill-building.

For v1, interviews are pre-scripted with branching dialogue trees.

---

## Phase 4: Prediction

**What happens:** Before the resolution, player must commit to predictions. This serves two purposes: it makes players accountable for their reasoning, and it generates calibration data.

**Player actions:**
1. Assign final probability estimates to each hypothesis
2. Indicate confidence level in your top pick
3. (In some cases) Predict specific details that would be true if you're right

**The Prediction Lock-In:**

```
┌─────────────────────────────────────────────────────────────────┐
│  PREDICTION LOCK-IN                               Case #AA-2847│
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Based on your investigation, what happened?                   │
│                                                                 │
│  Theory A: Marcus Flint deliberately cursed the victim  [70%]  │
│  Theory B: The curse was self-inflicted (accident)      [15%]  │
│  Theory C: Unknown third party responsible              [10%]  │
│  Theory D: Something else entirely                      [ 5%]  │
│                                                                 │
│  ─────────────────────────────────────────────────────────────  │
│                                                                 │
│  How confident are you in your top pick?                       │
│                                                                 │
│      ◄━━━━━━━━━━━━━━━━━●━━━━━━━━━━━━━━━━►                       │
│      Very              Moderately        Completely            │
│      Uncertain         Confident         Certain               │
│                                                                 │
│  If Theory A is correct, which of these should be true?        │
│  □ Marcus's wand will show matching curse signature            │
│  □ Marcus had clear motive (rivalry, debt, etc.)               │
│  □ Marcus has no alibi for the time of the incident            │
│                                                                 │
│                           [Lock In Predictions ►]              │
│         (You cannot change these after proceeding)             │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

**Calibration Scoring:**

Over many cases, the game tracks whether your confidence matches your accuracy:

- When you say 70%, are you right about 70% of the time?
- When you say 90%, are you right about 90% of the time?

Most people's 90% confidence intervals are correct only about 50% of the time. The game helps players discover their overconfidence and recalibrate.

```
YOUR CALIBRATION (after 10 cases)
─────────────────────────────────
Stated      Actual      
Confidence  Accuracy    Status
─────────────────────────────────
50%         45%         ✓ Well-calibrated
70%         52%         ⚠️ Overconfident  
90%         61%         ⚠️ Significantly overconfident

You tend to be overconfident. When you feel 90% sure,
you're actually right only about 60% of the time.
Consider: What would need to be true for you to be wrong?
```

---

## Phase 5: Resolution

**What happens:** The truth is revealed. Players see what actually happened, how it compares to their predictions, and where their reasoning succeeded or failed.

**Structure:**
1. **The Reveal** — Narrative explanation of what actually happened
2. **Prediction Results** — How player's estimates compared to truth
3. **Key Decision Points** — Review of critical moments where reasoning could have gone differently
4. **Bias Analysis** — Which cognitive biases affected the player's investigation

**Resolution Screen:**

```
┌─────────────────────────────────────────────────────────────────┐
│  CASE CLOSED                                      Case #AA-2847│
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  THE TRUTH:                                                     │
│  ══════════                                                     │
│  [Narrative explanation of what actually happened,             │
│   revealing the true culprit and their methods]                │
│                                                                 │
│  ─────────────────────────────────────────────────────────────  │
│                                                                 │
│  YOUR PREDICTIONS VS REALITY:                                   │
│                                                                 │
│  Theory A: Marcus Flint deliberately cursed the victim         │
│            Your estimate: 70%  │  Reality: ✗ FALSE             │
│                                                                 │
│  Theory C: Unknown third party responsible                     │
│            Your estimate: 10%  │  Reality: ✓ TRUE              │
│                                                                 │
│  Calibration Note: You assigned only 10% to the correct        │
│  answer. This happens! But notice: what evidence could         │
│  you have found that would have raised this probability?       │
│                                                                 │
│                    [Continue to Case Review ►]                 │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Phase 6: Case Review (Learning Consolidation)

**What happens:** After the case concludes, players receive a detailed breakdown of their reasoning process. This is where explicit learning happens—connecting gameplay experience to rationality concepts.

**Sections:**

**1. Investigation Pattern Analysis**
Shows confirmation bias score, evidence diversity, whether player sought discriminating evidence.

**2. Bias Identification**
Names the specific cognitive biases that may have affected the player, with examples from their choices.

**3. Key Learning Moment**
One focused insight from this case, connected to real-world application.

**4. The Technique**
Introduces or reinforces a specific rationality technique relevant to this case.

**5. Practice Prompt**
A suggestion for applying this technique in real life within the next 24 hours.

---

## Progression Systems

### Auror Rank
Players progress from Trainee → Junior Auror → Auror → Senior Auror → Head Auror. Higher ranks unlock:
- More complex cases with multiple interacting biases
- Cases where the "obvious" suspect is wrong
- Cases involving unreliable narrators and deceiving witnesses
- Cases where the player character has personal stakes

### Technique Mastery
Each rationality technique has a mastery level:
- **Novice:** Has encountered the technique
- **Apprentice:** Has successfully applied it once
- **Practitioner:** Applies it correctly most of the time
- **Master:** Applies it automatically and can teach others

### Calibration Score
Running accuracy of probability estimates. Players can see their improvement over time.

### Bias Resistance Rating
Tracks susceptibility to specific biases. Players can see which biases they're most vulnerable to and focus on improvement.

---

# Part III: Mission 1 — "The Vanishing Violinist"

## Mission Overview

**Case Number:** AA-2847  
**Classification:** Training Exercise (Level 1)  
**Primary Skills Taught:** Hypothesis generation, confirmation bias awareness  
**Secondary Skills:** Basic evidence evaluation, probability estimation  
**Estimated Duration:** 20-30 minutes

## Narrative Setup

Three days ago, Elara Thornwood—a famous wizarding violinist known for enchanted performances—collapsed during a private concert at the Malfoy Manor. She remains in St. Mungo's Hospital, unconscious and unresponsive to all magical treatment. The Healers have determined she was struck by a curse, but they cannot identify which one or how to counter it.

The Ministry has assigned this case to the Auror Training Division as a supervised exercise. The player, a first-week trainee, will investigate under the guidance of Senior Auror Moody (not Mad-Eye, but a different Moody—cousin, perhaps).

## Why This Case Works for Mission 1

**It teaches hypothesis generation** — There are multiple plausible suspects with different motives, and the true answer requires considering possibilities beyond the obvious.

**It introduces confirmation bias gently** — The case is structured so that pursuing the "obvious" suspect feels natural but leads to missing crucial evidence. Players who diversify their investigation find the truth more easily.

**It has emotional stakes without violence** — The victim is unconscious, not dead, which keeps the tone appropriate for an introduction while still feeling urgent.

**It's forgiving** — Even players who get it "wrong" can successfully complete the mission; they just receive lower scores and more detailed bias feedback.

---

## Phase 1: Briefing

### The Case File

```
═══════════════════════════════════════════════════════════════════
              MINISTRY OF MAGIC — AUROR DEPARTMENT
                    INCIDENT REPORT #AA-2847
═══════════════════════════════════════════════════════════════════

DATE OF INCIDENT: 14 November, 8:47 PM
LOCATION: Malfoy Manor, East Drawing Room
VICTIM: Elara Thornwood (age 34, witch, professional musician)
STATUS: Critical condition, St. Mungo's, Ward 49

───────────────────────────────────────────────────────────────────
SUMMARY OF INCIDENT:

Miss Thornwood was performing a private concert for approximately 
12 guests when she collapsed mid-performance. Witnesses report she 
stopped playing suddenly, clutched her head, and fell unconscious.
No visible curse light was observed. She has not regained 
consciousness despite Healer intervention.

HEALER'S PRELIMINARY REPORT:
"Curse damage consistent with close-range targeted hex, likely 
cast within the previous hour. Curse signature is unusual—does 
not match standard patterns in our diagnostic manuals. Victim 
shows no physical injuries."

───────────────────────────────────────────────────────────────────
INITIAL WITNESS STATEMENT (condensed):

LUCIUS MALFOY (host): "It was a perfectly normal evening until 
she collapsed. I have no idea who would want to harm her. She 
was mid-piece—playing beautifully—and then simply... stopped."

───────────────────────────────────────────────────────────────────
PERSONS OF INTEREST:

1. VICTOR ASHWORTH — Former romantic partner of victim. 
   Relationship ended acrimoniously 6 months ago. Was present 
   at the concert.

2. HELENA VANCE — Rival violinist who lost a prestigious 
   competition to victim last year. Was present at the concert.

3. NARCISSA MALFOY — Hostess. No known connection to victim 
   beyond hiring her for the performance.

───────────────────────────────────────────────────────────────────
ASSIGNED AURORS: Trainee [PLAYER NAME], supervised by Sr. Auror G. Moody

INVESTIGATION BUDGET: 7 Investigation Points

═══════════════════════════════════════════════════════════════════
```

### Senior Auror Moody's Introduction

> "Right then, trainee. Your first real case—well, real enough. The witch is in St. Mungo's, the curse is unknown, and we've got three possible suspects sitting pretty at Malfoy Manor.
>
> Now, I could tell you who I think did it. But that wouldn't teach you a thing, would it? So here's how this works: You investigate. You gather evidence. You tell me what you think happened. And then we'll see how good your thinking really is.
>
> One piece of advice before you start: It's easy to pick a suspect and then look for evidence that proves you right. Feels efficient. Feels smart. But it's a trap. The best Aurors I know? They look for evidence that would prove them *wrong*. If they can't find it, *then* they know they're onto something.
>
> Start by considering all the possibilities. Don't fall in love with a theory before you've done the legwork.
>
> You've got 7 Investigation Points. Spend them wisely."

---

## Phase 2: Hypothesis Formation

### The Hypothesis Board

The player must generate at least 3 hypotheses. For Mission 1, we provide a structured selection rather than free-form entry:

**Pre-written hypothesis options (player selects at least 3):**

**A. Victor Ashworth (the ex-partner) cast the curse**
*Motive: Revenge for the breakup. Classic "if I can't have her, no one can" pattern.*

**B. Helena Vance (the rival) cast the curse**
*Motive: Professional jealousy. Removing competition for future opportunities.*

**C. The curse was on the violin, not cast during the concert**
*Motive: Unknown. But would explain why no one saw curse-light. The curse could have been placed earlier.*

**D. Lucius Malfoy is involved somehow**
*Motive: Unknown, but the Malfoys have Dark Magic history. Worth considering.*

**E. The victim cursed herself (intentionally or accidentally)**
*Motive: Unclear. But should be considered.*

**F. An unknown person not on the guest list is responsible**
*Motive: Unknown. There may be suspects we haven't identified yet.*

**G. Something else entirely**
*Always available as an option—reminder that our hypotheses might all be wrong.*

### Design Note: The Trap

The briefing is written to make **Victor Ashworth (the ex)** the obvious suspect. He has clear motive (scorned lover), he was present, and "jealous ex" is a familiar narrative pattern. 

Most players will assign him the highest initial probability.

The *actual* answer is **C: the curse was on the violin itself**. But this hypothesis gets buried because:
- It's less narratively dramatic
- There's no obvious suspect attached to it
- The player has to think about *mechanism*, not just *motive*

This teaches that "obvious" suspects aren't always guilty, and that considering *how* something happened (not just *who* and *why*) is crucial.

### Probability Assignment

After selecting hypotheses, player assigns initial probability estimates:

```
Your Initial Assessment:
────────────────────────────────────────
A. Victor Ashworth (jealous ex)    [___]%
B. Helena Vance (rival violinist)  [___]%
C. Curse on the violin             [___]%
D. Lucius Malfoy involved          [___]%
G. Something else                  [___]%
                                   ──────
                          Total:   [100]%
```

---

## Phase 3: Investigation

### Available Investigation Actions

Player has **7 Investigation Points** to spend. Available actions:

```
LOCATION INVESTIGATIONS
═══════════════════════════════════════════════════════════════════

[2 IP] EXAMINE THE CRIME SCENE (East Drawing Room)
       Search the room where the collapse occurred. Look for 
       magical residue, physical evidence, signs of tampering.

[2 IP] EXAMINE THE VIOLIN
       The victim's instrument has been secured. Perform detailed 
       magical analysis of the violin itself.

[2 IP] SEARCH VICTOR ASHWORTH'S QUARTERS
       He's been staying at a nearby inn. Search for evidence of 
       curse preparation, Dark artifacts, or other incriminating items.

───────────────────────────────────────────────────────────────────
WITNESS INTERVIEWS
═══════════════════════════════════════════════════════════════════

[2 IP] INTERVIEW VICTOR ASHWORTH
       The ex-partner. Last saw victim 6 months ago... or did he?

[2 IP] INTERVIEW HELENA VANCE  
       The rival violinist. Competitive relationship with victim.

[1 IP] INTERVIEW LUCIUS MALFOY
       The host. Knows the guest list and household arrangements.

[1 IP] INTERVIEW ORCHESTRA MEMBERS
       Other musicians who performed alongside the victim.

───────────────────────────────────────────────────────────────────
RECORDS & RESEARCH
═══════════════════════════════════════════════════════════════════

[1 IP] MINISTRY RECORDS: Victor Ashworth
       Criminal background, wand registration, past incidents.

[1 IP] MINISTRY RECORDS: Helena Vance
       Criminal background, wand registration, past incidents.

[1 IP] RESEARCH: Violin history
       Trace the instrument's provenance and recent handling.

[1 IP] ST. MUNGO'S CONSULTATION
       Speak with Healers about the specific curse characteristics.

═══════════════════════════════════════════════════════════════════
```

### Evidence Outcomes

**EXAMINE THE CRIME SCENE (2 IP)**
```
┌─────────────────────────────────────────────────────────────────┐
│ EVIDENCE COLLECTED: Crime Scene Analysis                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ You examine the East Drawing Room where the collapse occurred.  │
│                                                                 │
│ FINDINGS:                                                       │
│ • No residual wand-magic detected in the room                   │
│ • No hidden Dark artifacts found                                │
│ • Chairs arranged in concert formation; no signs of struggle    │
│ • The window was closed and locked from inside                  │
│ • A faint magical residue lingers near the performance spot,    │
│   but it's unclear if this is from the curse or normal          │
│   performance magic                                             │
│                                                                 │
│ INTERPRETATION:                                                 │
│ If someone cast a curse during the performance, they either     │
│ used wandless magic (rare) or the curse was somehow delayed     │
│ or indirect. There's no sign of a direct wand-based attack      │
│ from the audience.                                              │
│                                                                 │
│ [This evidence is DISCRIMINATING - it weakens theories          │
│  involving someone casting a curse during the concert]          │
└─────────────────────────────────────────────────────────────────┘
```

**EXAMINE THE VIOLIN (2 IP)** [CRITICAL EVIDENCE]
```
┌─────────────────────────────────────────────────────────────────┐
│ EVIDENCE COLLECTED: Violin Analysis                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ You cast diagnostic spells on the victim's violin.              │
│                                                                 │
│ FINDINGS:                                                       │
│ • The violin itself carries a strong curse residue              │
│ • The curse appears to be embedded in the rosin on the bow      │
│ • Analysis indicates the curse was designed to activate after   │
│   prolonged contact—approximately 45 minutes of playing         │
│ • The curse signature is complex, suggesting significant        │
│   Dark Arts knowledge                                           │
│ • The rosin was recently applied—within the past week           │
│                                                                 │
│ INTERPRETATION:                                                 │
│ The curse was delivered through the violin, not cast during     │
│ the performance. Someone with access to the instrument          │
│ cursed the rosin. The victim was doomed the moment she          │
│ began playing.                                                  │
│                                                                 │
│ [This evidence is CRITICAL - strongly supports the              │
│  "curse on the violin" hypothesis]                              │
└─────────────────────────────────────────────────────────────────┘
```

**INTERVIEW VICTOR ASHWORTH (2 IP)**
```
┌─────────────────────────────────────────────────────────────────┐
│ EVIDENCE COLLECTED: Victor Ashworth Interview                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Victor Ashworth appears genuinely distressed. He speaks        │
│ quickly and defensively.                                        │
│                                                                 │
│ KEY STATEMENTS:                                                 │
│                                                                 │
│ "Yes, we ended badly. Yes, I was angry. But that was months    │
│ ago. I came tonight because... I still care about her. I       │
│ wanted to hear her play one more time. Is that so strange?"    │
│                                                                 │
│ "I was in the back of the room the entire concert. At least    │
│ five people can confirm that. I never moved from my seat."     │
│                                                                 │
│ "I haven't touched a Dark curse in my life. Check my wand if   │
│ you want. I'm a Potions supplier, for Merlin's sake."          │
│                                                                 │
│ OBSERVATIONS:                                                   │
│ • His wand shows no recent curse activity                       │
│ • His alibi for the time of the concert appears solid          │
│ • He does seem to have lingering feelings for the victim       │
│                                                                 │
│ [This evidence WEAKENS the Victor Ashworth hypothesis]          │
└─────────────────────────────────────────────────────────────────┘
```

**INTERVIEW HELENA VANCE (2 IP)**
```
┌─────────────────────────────────────────────────────────────────┐
│ EVIDENCE COLLECTED: Helena Vance Interview                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Helena Vance is calm and collected. Perhaps too calm?          │
│                                                                 │
│ KEY STATEMENTS:                                                 │
│                                                                 │
│ "Elara and I are competitors, yes. But that's professional,    │
│ not personal. I don't wish her harm. The music world is        │
│ richer with both of us in it."                                 │
│                                                                 │
│ "I was seated near the front, in full view of everyone. I      │
│ didn't cast anything—anyone would have seen."                  │
│                                                                 │
│ "Have you examined her violin? Musicians are very particular   │
│ about their instruments. She would never let anyone else       │
│ touch it... normally."                                         │
│                                                                 │
│ OBSERVATIONS:                                                   │
│ • She volunteered the violin theory unprompted (interesting)   │
│ • Her alibi during the concert seems solid                      │
│ • She showed no obvious signs of deception                     │
│                                                                 │
│ [This evidence is SUGGESTIVE but not conclusive]               │
└─────────────────────────────────────────────────────────────────┘
```

**RESEARCH: VIOLIN HISTORY (1 IP)** [CRITICAL EVIDENCE]
```
┌─────────────────────────────────────────────────────────────────┐
│ EVIDENCE COLLECTED: Violin Provenance Research                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ You trace the recent history of Elara Thornwood's violin.      │
│                                                                 │
│ FINDINGS:                                                       │
│ • The violin was serviced at "Marchetti's Magical Instruments" │
│   five days before the concert                                 │
│ • Standard service includes restringing and fresh rosin        │
│ • The shop owner, Orion Marchetti, handled the instrument      │
│ • Cross-referencing: Orion Marchetti filed for bankruptcy      │
│   last year after a legal dispute with Elara Thornwood over    │
│   payment for a custom instrument                              │
│ • Thornwood won the dispute; Marchetti lost his savings        │
│                                                                 │
│ NEW PERSON OF INTEREST IDENTIFIED:                             │
│ Orion Marchetti — instrument shop owner with financial         │
│ motive and direct access to the violin's rosin                 │
│                                                                 │
│ [This evidence is CRITICAL - identifies a new suspect with     │
│  means, motive, and opportunity]                               │
└─────────────────────────────────────────────────────────────────┘
```

**ST. MUNGO'S CONSULTATION (1 IP)**
```
┌─────────────────────────────────────────────────────────────────┐
│ EVIDENCE COLLECTED: Healer's Technical Assessment             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ You speak with the Healer treating Elara Thornwood.            │
│                                                                 │
│ KEY INFORMATION:                                                │
│                                                                 │
│ "The curse is sophisticated—designed to incapacitate rather    │
│ than kill. It's almost... professional. Someone who wanted     │
│ her dead would have used something more direct."               │
│                                                                 │
│ "The entry point for the curse is unusual. It came through     │
│ prolonged skin contact—through her hands, we think. Not a      │
│ spell cast at her, but something she was holding."             │
│                                                                 │
│ "If you can find the source object and reverse-engineer the    │
│ curse, we have a much better chance of waking her."            │
│                                                                 │
│ [This evidence SUPPORTS the cursed-object hypothesis]          │
└─────────────────────────────────────────────────────────────────┘
```

**MINISTRY RECORDS: VICTOR ASHWORTH (1 IP)**
```
┌─────────────────────────────────────────────────────────────────┐
│ EVIDENCE COLLECTED: Victor Ashworth Background                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Official Ministry records for Victor Ashworth.                 │
│                                                                 │
│ CRIMINAL HISTORY: None                                         │
│ WAND REGISTRATION: Elm, unicorn hair, registered age 11        │
│ OCCUPATION: Licensed Potions supplier, no violations           │
│ DARK ARTS HISTORY: No known associations                       │
│                                                                 │
│ NOTES:                                                          │
│ Mr. Ashworth has a clean record. His relationship with Miss    │
│ Thornwood ended in May; she initiated the separation citing    │
│ "incompatible life goals." No reports of harassment or         │
│ concerning behavior since.                                     │
│                                                                 │
│ [This evidence WEAKENS the Victor Ashworth hypothesis—         │
│  clean record, no Dark Arts background]                        │
└─────────────────────────────────────────────────────────────────┘
```

**MINISTRY RECORDS: HELENA VANCE (1 IP)**
```
┌─────────────────────────────────────────────────────────────────┐
│ EVIDENCE COLLECTED: Helena Vance Background                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Official Ministry records for Helena Vance.                    │
│                                                                 │
│ CRIMINAL HISTORY: None                                         │
│ WAND REGISTRATION: Willow, phoenix feather, registered age 11  │
│ OCCUPATION: Professional musician, no violations               │
│ DARK ARTS HISTORY: No known associations                       │
│                                                                 │
│ NOTES:                                                          │
│ Miss Vance competed against Thornwood for the Celestina Prize  │
│ last year and lost. Some gossip columns reported tension,      │
│ but no official complaints from either party.                  │
│                                                                 │
│ [This evidence is NEUTRAL - neither supports nor weakens       │
│  the Helena Vance hypothesis significantly]                    │
└─────────────────────────────────────────────────────────────────┘
```

**INTERVIEW LUCIUS MALFOY (1 IP)**
```
┌─────────────────────────────────────────────────────────────────┐
│ EVIDENCE COLLECTED: Lucius Malfoy Interview                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Lucius Malfoy is cooperative but measured in his responses.    │
│                                                                 │
│ KEY STATEMENTS:                                                 │
│                                                                 │
│ "I hired Miss Thornwood through her manager. We've had no      │
│ prior interaction. I simply wanted quality entertainment       │
│ for our guests."                                                │
│                                                                 │
│ "The guest list was quite small—intimate gathering. I can      │
│ provide full names if needed. No one entered or left           │
│ during the performance."                                        │
│                                                                 │
│ "Her violin? She brought it herself, of course. It never       │
│ left her sight while she was here. She's quite protective      │
│ of it."                                                         │
│                                                                 │
│ [This evidence establishes the violin was brought              │
│  by the victim herself—relevant to cursed-object theory]       │
└─────────────────────────────────────────────────────────────────┘
```

**INTERVIEW ORCHESTRA MEMBERS (1 IP)**
```
┌─────────────────────────────────────────────────────────────────┐
│ EVIDENCE COLLECTED: Orchestra Member Interviews                │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ You speak with the three other musicians who performed.        │
│                                                                 │
│ CELLIST (Margaret Hollow):                                      │
│ "Elara seemed fine before the performance. A little nervous    │
│ maybe—she mentioned something about her violin feeling         │
│'different' after the servicing. New strings, I assumed."       │
│                                                                 │
│ PIANIST (Theodore Mills):                                       │
│ "I was focused on my own playing. Didn't notice anything       │
│ unusual until she collapsed. No one in the audience moved      │
│ suspiciously."                                                  │
│                                                                 │
│ FLAUTIST (Anna Chen):                                          │
│ "Where did she get her violin serviced? Marchetti's, I think.  │
│ Same place I go. Odd man, but good with instruments."          │
│                                                                 │
│ [This evidence SUPPORTS the violin/servicing theory]           │
└─────────────────────────────────────────────────────────────────┘
```

**SEARCH VICTOR ASHWORTH'S QUARTERS (2 IP)**
```
┌─────────────────────────────────────────────────────────────────┐
│ EVIDENCE COLLECTED: Ashworth's Inn Room Search                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ You search Victor Ashworth's room at the local inn.            │
│                                                                 │
│ FINDINGS:                                                       │
│ • Standard traveling supplies, nothing suspicious              │
│ • A bundle of letters from Elara Thornwood, kept neatly        │
│ • A pressed flower in a book of poetry (sentimental)           │
│ • His wand shows no recent Dark magic                          │
│ • No curse preparation materials, no Dark artifacts            │
│ • A concert program from the previous night, with              │
│   "Good luck, E" written in the margin in his handwriting      │
│                                                                 │
│ INTERPRETATION:                                                 │
│ He appears to still have feelings for her. This looks like     │
│ a man who came to see someone he misses, not to attack them.   │
│                                                                 │
│ [This evidence WEAKENS the Victor Ashworth hypothesis]         │
└─────────────────────────────────────────────────────────────────┘
```

---

### Investigation Strategy Analysis

**The "confirmation bias trap" path:**
A player who fixates on Victor Ashworth (the obvious jealous ex) might spend:
- 2 IP: Interview Victor Ashworth
- 2 IP: Search Victor's quarters  
- 1 IP: Ministry records on Victor
- 2 IP: Crime scene examination

Total: 7 IP spent, all weakening their favorite hypothesis but never finding the true answer (they missed the violin and violin history).

**The "discriminating evidence" path:**
A player thinking about mechanism, not just motive, might spend:
- 1 IP: St. Mungo's consultation (reveals it's a contact curse)
- 2 IP: Examine the violin (confirms curse on instrument)
- 1 IP: Research violin history (identifies Marchetti)
- 1 IP: Interview orchestra members (confirms Marchetti connection)

Total: 5 IP spent, case essentially solved. 2 IP remaining for confirmation.

**The game doesn't punish the first player harshly**—they can still succeed, but their scores will be lower and they'll receive more detailed feedback about confirmation bias.

---

## Phase 4: Prediction

After investigation, the player locks in predictions:

```
┌─────────────────────────────────────────────────────────────────┐
│  PREDICTION LOCK-IN                               Case #AA-2847│
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Based on your investigation, what happened?                   │
│                                                                 │
│  A. Victor Ashworth cursed the victim               [___]%     │
│  B. Helena Vance cursed the victim                  [___]%     │
│  C. The violin was cursed by someone else           [___]%     │
│  D. Lucius Malfoy is responsible                    [___]%     │
│  E. Self-inflicted or accident                      [___]%     │
│  F. Something else entirely                         [___]%     │
│                                                     ──────     │
│                                           Total:   [100]%      │
│                                                                 │
│  ─────────────────────────────────────────────────────────────  │
│                                                                 │
│  If you found evidence about the violin (Hypothesis C),        │
│  who do you believe cursed it?                                  │
│                                                                 │
│  ○ Orion Marchetti (shop owner with grudge)                    │
│  ○ Helena Vance (planted during servicing)                     │
│  ○ Victor Ashworth (broke into the shop)                       │
│  ○ Unknown third party                                          │
│  ○ I didn't investigate the violin                             │
│                                                                 │
│                          [Lock In Predictions ►]               │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Phase 5: Resolution

### The Truth Revealed

```
═══════════════════════════════════════════════════════════════════
                         CASE CLOSED
                    THE VANISHING VIOLINIST
═══════════════════════════════════════════════════════════════════

THE TRUTH:

Orion Marchetti, owner of Marchetti's Magical Instruments, cursed 
Elara Thornwood's violin rosin during a routine servicing five 
days before the concert.

Marchetti lost his life savings in a legal dispute with Thornwood 
two years ago, when she refused to pay for a custom violin she 
claimed was defective. The courts sided with Thornwood. Marchetti 
never recovered financially—or emotionally.

When Thornwood brought her violin in for servicing, Marchetti 
saw his opportunity. He spent four days preparing a sophisticated 
incapacitation curse, embedded it in the rosin, and applied it 
to her bow. The curse was designed to activate after approximately 
45 minutes of contact—enough time to ensure she was far from the 
shop when it triggered.

Marchetti has been apprehended and charged with assault with 
intent to harm. The curse signature from the rosin matches 
his wand. He has confessed.

Elara Thornwood is expected to make a full recovery now that 
Healers understand the curse's composition.

───────────────────────────────────────────────────────────────────

WHAT MADE THIS CASE TRICKY:

The briefing emphasized Victor Ashworth (the jealous ex) and 
Helena Vance (the professional rival). Both were present at the 
concert and had apparent motives.

But the true culprit wasn't at the concert at all. He didn't 
need to be—he cursed the instrument days earlier.

Solving this case required thinking about MECHANISM, not just 
MOTIVE. The question "How was the curse delivered?" led to 
the violin. The question "Who touched the violin recently?" 
led to Marchetti.

═══════════════════════════════════════════════════════════════════
```

---

## Phase 6: Case Review

### Investigation Pattern Analysis

```
YOUR INVESTIGATION PATTERN
═══════════════════════════════════════════════════════════════════

Investigation Points Spent by Hypothesis Relevance:

Victor Ashworth theories:   ████████░░░░░  5 IP (71%)
Helena Vance theories:      ██░░░░░░░░░░░  1 IP (14%)
Violin/mechanism theories:  █░░░░░░░░░░░░  1 IP (14%)
Other theories:             ░░░░░░░░░░░░░  0 IP (0%)

[If applicable:]
⚠️ CONFIRMATION BIAS DETECTED

You spent 71% of your investigation resources on evidence 
related to Victor Ashworth—your initial leading suspect.

This is a common pattern. Once we have a "favorite" theory, 
we tend to look for evidence that confirms it rather than 
evidence that would test it against alternatives.

WHAT HAPPENED:
Evidence about Victor mostly WEAKENED that hypothesis (clean 
record, solid alibi, no Dark Arts history). But without 
investing in alternative theories, you never found the 
discriminating evidence that pointed to the true answer.

───────────────────────────────────────────────────────────────────

[If player investigated broadly:]
✓ WELL-DIVERSIFIED INVESTIGATION

You spread your investigation across multiple hypotheses. This 
allowed you to find discriminating evidence—information that 
distinguishes between theories rather than just confirming 
your favorite.

The key evidence (violin analysis + violin history) pointed 
directly to Marchetti, even though he wasn't on the original 
suspect list.

═══════════════════════════════════════════════════════════════════
```

### The Bias Breakdown

```
BIASES IN THIS CASE
═══════════════════════════════════════════════════════════════════

1. AVAILABILITY HEURISTIC
   "Jealous ex-partner" is a familiar story pattern. When you 
   hear about a woman being attacked, your mind quickly suggests 
   "check the ex-boyfriend." This pattern is *sometimes* right—
   but its availability in memory makes it feel more likely than 
   statistics would support.

2. CONFIRMATION BIAS  
   Once Victor Ashworth seemed promising, it felt efficient to 
   investigate him thoroughly. But "thoroughly investigating 
   one suspect" isn't the same as "investigating well." The 
   best investigation spreads effort across hypotheses.

3. ANCHORING
   The briefing mentioned three suspects by name. This "anchored" 
   thinking to those three possibilities, making it harder to 
   consider that the true culprit might not be on the list.

───────────────────────────────────────────────────────────────────

THE COUNTER-TECHNIQUE: "WHAT WOULD DISTINGUISH?"

Before investigating any single lead, ask: "What evidence would 
tell me whether Hypothesis A or Hypothesis B is true?"

In this case, that question leads directly to mechanism. If 
someone in the room cast the curse, there'd be wand residue. 
If the curse was on an object, the object would show magical 
traces. This discriminating evidence was available for just 
2 IP (examine the violin)—but only if you thought to look.

═══════════════════════════════════════════════════════════════════
```

### Real-World Connection

```
REAL-WORLD APPLICATION
═══════════════════════════════════════════════════════════════════

The "obvious suspect" trap applies everywhere:

IN THE WORKPLACE:
When a project fails, it's easy to blame the person who seems 
most stereotypically responsible—the new hire, the contractor, 
the team with a previous failure. But systematic analysis often 
reveals causes that weren't on anyone's initial list.

IN RELATIONSHIPS:
When conflict arises, we often assume we understand the other 
person's motives ("they're just being selfish"). But the real 
cause might be something we haven't considered—something we'd 
only discover by asking "What would have to be true for their 
behavior to make sense?"

IN CURRENT EVENTS:
When something goes wrong in the news, narratives emerge quickly. 
"This happened because of [political party / group / individual]." 
Often, the real explanation is more complex, but the availability 
of familiar stories makes the simple narrative feel true.

───────────────────────────────────────────────────────────────────

YOUR PRACTICE PROMPT:

Think of a recent situation where you were confident about 
someone's motives or the cause of a problem.

Ask yourself: "What's an alternative explanation I haven't 
seriously considered? What evidence would distinguish between 
my current theory and this alternative?"

═══════════════════════════════════════════════════════════════════
```

---

# Part IV: Technical Requirements

## Core Technologies

**Recommended Stack:**
- **Frontend:** React + TypeScript (strong typing helps with game state management)
- **Styling:** Tailwind CSS + custom theming for the "magical parchment" aesthetic
- **State Management:** Zustand or Redux (game state can get complex)
- **Backend (v2):** Node.js + Express for user accounts and progress tracking
- **LLM Integration (v2):** Claude API for dynamic interviews

## For MVP (v1): Static Content, Client-Side Only

The first version can be fully client-side with pre-written content:

```
/src
  /components
    /Briefing           # Case file display
    /HypothesisBoard    # Hypothesis selection and probability sliders
    /Investigation      # Evidence selection and display
    /Prediction         # Lock-in predictions
    /Resolution         # Truth reveal and scoring
    /CaseReview         # Bias analysis and learning
  /data
    /cases
      mission1.json     # All content for Mission 1
  /hooks
    useGameState.ts     # Central game state management
  /utils
    scoring.ts          # Calibration and confirmation bias calculations
```

## Key Data Structures

```typescript
// Core case definition
interface Case {
  id: string;
  title: string;
  briefing: Briefing;
  hypotheses: Hypothesis[];
  investigationActions: InvestigationAction[];
  truth: Resolution;
  biasAnalysis: BiasAnalysis[];
}

// Hypothesis tracking
interface Hypothesis {
  id: string;
  label: string;
  description: string;
  isCorrect: boolean;
  relatedActionIds: string[]; // Which actions provide evidence for this?
}

// Investigation action with evidence
interface InvestigationAction {
  id: string;
  title: string;
  cost: number; // Investigation Points
  category: 'location' | 'witness' | 'records';
  evidence: Evidence;
  hypothesisRelevance: { [hypothesisId: string]: 'supports' | 'weakens' | 'neutral' };
}

// Player state
interface PlayerState {
  currentPhase: 'briefing' | 'hypothesis' | 'investigation' | 'prediction' | 'resolution' | 'review';
  selectedHypotheses: string[];
  probabilityEstimates: { [hypothesisId: string]: number }; // Initial estimates
  investigationPointsRemaining: number;
  collectedEvidence: string[]; // IDs of collected evidence
  finalPredictions: { [hypothesisId: string]: number };
  confidenceLevel: number;
}
```

## Scoring Algorithms

**Confirmation Bias Score:**
```typescript
function calculateConfirmationBias(
  actions: string[], 
  topHypothesis: string,
  actionRelevance: Map<string, string[]>
): number {
  const totalActions = actions.length;
  const actionsForTopHypothesis = actions.filter(
    a => actionRelevance.get(a)?.includes(topHypothesis)
  ).length;
  
  // Perfect distribution would be 1/numHypotheses
  // Score is 0-100 where 100 = total confirmation bias
  return (actionsForTopHypothesis / totalActions) * 100;
}
```

**Calibration Score:**
```typescript
// Track over multiple cases
interface CalibrationData {
  predictions: { confidence: number; wasCorrect: boolean }[];
}

function calculateCalibrationScore(data: CalibrationData): CalibrationResult {
  // Group predictions by confidence bucket (e.g., 50-60%, 60-70%, etc.)
  // Compare stated confidence to actual accuracy
  // Perfect calibration: 70% confidence predictions are right 70% of the time
}
```

## LLM Integration Points (v2)

When ready to add LLM capabilities:

**1. Dynamic Witness Interviews**
Instead of pre-scripted dialogue trees, players can ask free-form questions:

```typescript
async function interviewWitness(
  witnessId: string, 
  playerQuestion: string
): Promise<WitnessResponse> {
  const witnessContext = getWitnessContext(witnessId); // personality, knowledge, secrets
  
  const response = await claude.messages.create({
    model: "claude-sonnet-4-20250514",
    system: `You are ${witnessContext.name}, a witness in a magical crime investigation. 
    ${witnessContext.personality}
    ${witnessContext.knowledge}
    ${witnessContext.secrets} (do not reveal directly, but can hint if pressed)
    
    Stay in character. Answer only what this character would know or say.
    Be somewhat guarded—this is an investigation, after all.`,
    messages: [{ role: "user", content: playerQuestion }]
  });
  
  return parseWitnessResponse(response);
}
```

**2. Dynamic Hypothesis Evaluation**
Allow players to type their own hypotheses; LLM evaluates if they're genuinely distinct:

```typescript
async function evaluateHypotheses(hypotheses: string[]): Promise<HypothesisAnalysis> {
  const response = await claude.messages.create({
    model: "claude-sonnet-4-20250514",
    system: `Analyze these investigation hypotheses. Determine:
    1. Are they meaningfully distinct or restatements of each other?
    2. What evidence would distinguish between them?
    3. Are there obvious alternatives the investigator missed?`,
    messages: [{ role: "user", content: hypotheses.join("\n") }]
  });
  
  return parseHypothesisAnalysis(response);
}
```

**3. Personalized Case Review**
Generate customized feedback based on the specific pattern of player decisions:

```typescript
async function generatePersonalizedReview(
  playerActions: PlayerState,
  caseData: Case
): Promise<PersonalizedFeedback> {
  const response = await claude.messages.create({
    model: "claude-sonnet-4-20250514",
    system: `You are a Senior Auror providing feedback to a trainee. 
    Be encouraging but honest. Focus on specific decisions they made 
    and what they could have done differently.`,
    messages: [{ 
      role: "user", 
      content: `Case: ${caseData.title}
      Player's investigation: ${JSON.stringify(playerActions)}
      True answer: ${caseData.truth}
      
      Provide personalized feedback on their investigation.`
    }]
  });
  
  return parseFeedback(response);
}
```

---

# Part V: Visual Design Guidelines

## Overall Aesthetic

The game should feel like you're handling actual Ministry of Magic documents. Think:
- Aged parchment textures (but readable—not too distressed)
- Wax seals and official stamps
- Quill-written fonts for headers (but standard fonts for body text—accessibility matters)
- Magical elements: subtle shimmer effects, moving photo-style illustrations
- Color palette: Deep burgundy, navy blue, gold accents, cream/parchment backgrounds

## Key UI Patterns

**Case Files:** Appear as actual documents you're flipping through
**Evidence:** Collected in an "evidence bag" sidebar that persists during investigation
**Hypothesis Board:** A cork board with pushpins and string connecting evidence to theories
**Probability Sliders:** Styled as magical measuring instruments
**Progress:** A wax seal that fills as you progress through phases

## Accessibility Requirements

- All magical fonts must have fallbacks
- Color is never the only indicator of meaning
- All images have alt text
- Keyboard navigation throughout
- Screen reader compatible evidence descriptions

---

# Appendix: Content Expansion

## Future Missions (Briefs)

**Mission 2: "The Cursed Correspondence"**
A Ministry official receives threatening letters that seem to predict the future. Teaches: distinguishing correlation from causation, the planning fallacy.

**Mission 3: "The Vanishing Vault"**
A Gringotts vault is emptied despite all security measures. Multiple suspects, each with alibi gaps. Teaches: reference class forecasting, updating on evidence.

**Mission 4: "The Divided Department"**
Two factions in the Department of Magical Creatures accuse each other of sabotage. Neither is fully honest. Teaches: Double Crux, finding shared cruxes in disagreements.

**Mission 5: "The Memory Manipulation"**
A witness's memory has been tampered with. What they remember contradicts physical evidence. Teaches: distinguishing memory from reality, triangulating sources.

---

# Quick Start Implementation Checklist

For MVP launch, prioritize:

- [ ] Briefing component with case file display
- [ ] Hypothesis board with probability sliders
- [ ] Investigation action selector with IP tracking
- [ ] Evidence display cards
- [ ] Prediction lock-in screen
- [ ] Resolution reveal with scoring
- [ ] Basic case review with confirmation bias analysis
- [ ] Mission 1 content fully written
- [ ] Core scoring algorithms implemented
- [ ] Basic visual theme applied

Defer to v2:
- [ ] User accounts and progress persistence
- [ ] Multiple cases
- [ ] LLM-powered interviews
- [ ] Calibration tracking across cases
- [ ] Achievement/mastery systems
- [ ] Mobile responsiveness