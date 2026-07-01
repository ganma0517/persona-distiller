# persona-distiller

A Claude Code skill that distills anyone's thinking style into a deployable, structured agent persona.

> Install once. Call with `幫我蒸餾 X` or `建立 X 的 agent persona`. Outputs a ready-to-deploy `PERSONA.md`.

---

## What It Does

Instead of writing a vague system prompt like "respond like X," this skill extracts **actionable cognitive structures** from source materials — then compresses them into a `PERSONA.md` with concrete if-then rules, deployable as any agent's system prompt.

---

## Two Distillation Modes

### 主動蒸餾 (Active) — subject participates

Used when the person being distilled can fill out a questionnaire or upload their own materials.

**Bias**: self-presentation bias (conscious self-description ≠ instinctive behavior)
**Confidence ceiling**: `medium` — always, regardless of material volume

**Tools**:
- CV / résumé → identity card and Metadata only (not for Layer 1–4 extraction)
- Self-uploaded articles → extracted with "academic performance" flag
- Structured questionnaire → see below

### 被動蒸餾 (Passive) — third-party materials

Used for public figures, historical subjects, or when a third party provides the source materials.

**Bias**: selection bias (available ≠ complete); performative bias (public texts are audience-aware)
**Confidence**: up to `high` (3+ media types × 5–15 year span × all validation tests pass)

**Tools**: writings, interview transcripts, speeches, third-party profiles

---

## Four-Layer Persona Structure

| Layer | Name | Content |
|-------|------|---------|
| 1 | Expression DNA | Opening preference, sentence patterns, signature vocabulary, taboo phrases |
| 2 | Mental Models | 3–6 frameworks this person uses to understand the world |
| 3 | Decision Heuristics | 5–8 if-then rules with priority numbers and conflict annotations |
| 4 | Values & Defense Patterns | Priority order, hard limits, defensive response style |
| — | Metadata (dev-facing) | What was distilled, what couldn't be, bias notes, validation results |

> Layer 5 ("Honest Limits") was reclassified as Metadata — it's a developer report, not an in-character cognitive structure.

---

## Structured Questionnaire (Active Mode)

The questionnaire is split into **base questions** + **optional professional modules**.

### Base Questions

**Multiple-choice (Layer 2–3)**

| Q | Question | Maps to |
|---|----------|---------|
| Q1 | First action when facing a completely unfamiliar problem? | Layer 2: information-gathering model |
| Q2 | First thing you check when evaluating someone else's research design? | Layer 2: evaluation model |
| Q3 | Prioritization basis when three urgent tasks compete? | Layer 3: triage heuristic |
| Q4 | How do you decide when uncertain about method choice? | Layer 3: decision under uncertainty |

**Open-ended (Layer 2–4 depth)**

- Q5: Your personal criteria for a research question being "worth doing"
- Q6: First step when you fundamentally disagree with a reviewer
- Q7: The one thing you absolutely won't compromise on in your work
- Q8: What kind of collaboration or request you'd decline outright
- Q9: Your first reaction when someone publicly criticizes your work

**Expression DNA supplement (optional)**

- Q10: A phrase or expression you find yourself using most often
- Q11: What communication style you find most difficult to work with

---

### Advanced Modules (optional, pick 1–4)

Each module is independent. Select based on the persona type: researchers → A+B; managers/collaborators → C+D.

#### Module A: Cognitive Style → Layer 2
*Adapted from Need for Cognition Scale (Cacioppo & Petty 1982) and Need for Closure Scale (Webster & Kruglanski 1994)*

| Q | Question |
|---|----------|
| A1 | When facing a complex problem, you tend to...? |
| A2 | When a conclusion feels "off," you usually...? |
| A3 | How do you feel about ambiguous answers with no clear right or wrong? |

Maps to: analytical vs. intuitive thinking; high/low need for cognitive closure

#### Module B: Regulatory Focus → Layer 3
*Adapted from Regulatory Focus Questionnaire (Higgins 1997)*

| Q | Question |
|---|----------|
| B1 | What is your primary drive when starting a new project? |
| B2 | Which type of failure do you fear more? |
| B3 | When you receive a medium-risk, good-opportunity invitation, you...? |

Maps to: Promotion Focus (opportunity-seeking) vs. Prevention Focus (loss-aversion) in Layer 3 heuristics

#### Module C: Conflict & Collaboration Style → Layer 3 + Layer 4
*Adapted from Thomas-Kilmann Conflict Mode Instrument (TKI). Adaptation: situational choice format replaces the original forced-ranking design; only the highest-discriminating conflict mode items retained.*

| Q | Question |
|---|----------|
| C1 | When you disagree with a collaborator, you tend to...? |
| C2 | What is your most comfortable collaboration structure? |
| C3 | What kind of person do you find most difficult to work with? |

Maps to: C1/C2 → Layer 3 (conditional behavior heuristics under conflict); C3 → Layer 4 (hard limits on collaboration)

#### Module D: Political-Moral Intuitions → Layer 4
*Adapted from Moral Foundations Theory (Haidt et al. 2009)*

| Q | Question |
|---|----------|
| D1 | Your basic attitude toward social rules? |
| D2 | Your first reaction when you witness injustice? |
| D3 | Where does your political intuition broadly fall? |

Maps to: values baseline, rule-orientation, action style background

---

## Workflow

```
                    ┌─────────────────────────────┐
                    │     模式選擇 / Mode Select      │
                    └───────────┬─────────────────┘
               ┌────────────────┴────────────────┐
               ▼                                 ▼
    【主動蒸餾 / Active】               【被動蒸餾 / Passive】
    Subject participates               Third-party materials
               │                                 │
    CV + Questionnaire           Source inventory + 80/20 split
    (Q1–Q11 + Modules A–D)       ↓
               │            Triple filter
               │            (consistency × predictive power × exclusivity)
               │                                 │
               └──────────────┬──────────────────┘
                              ▼
                   Four-Layer Extraction
                   Layer 1: Expression DNA
                   Layer 2: Mental Models
                   Layer 3: Decision Heuristics
                   Layer 4: Values & Defense
                              │
                              ▼
                   Validation Tests
                   (known Q / unknown Q / stress test)
                              │
                              ▼
                   PERSONA.md  ←── confidence: high / medium / low
                              │
                              ▼
               System Prompt deployment version
               (compressed, paste directly into any agent)
```

---

## Confidence Levels

| Level | Criteria |
|-------|----------|
| `high` | 3+ media types × 5–15 year span × all validation tests pass |
| `medium` | 2 media types OR span < 5 years or > 15 years OR 1 test failed; **always the ceiling for Active mode** |
| `low` | Single source OR multiple tests unstable |

> "Time span" = range of years covered by materials, not how old they are. Materials spanning > 15 years may not reflect current state → also `medium`.

---

## Installation

This is a [Claude Code](https://claude.ai/code) skill. Place the skill directory under your Claude skills folder:

```bash
# Clone into your Claude skills directory
git clone https://github.com/ganma0517/persona-distiller ~/.claude/skills/persona-distiller
```

Then restart Claude Code. The skill activates automatically when you say:

- `幫我蒸餾 X`
- `建立 X 的 agent persona`
- `我要一個像 X 的助理設定`
- `distill X's thinking style`

---

## Output Format

The skill produces a `PERSONA.md` with:

1. YAML frontmatter (persona name, sources, confidence level)
2. One-line positioning statement
3. Expression DNA section
4. Mental Models (3–6 entries, each with trigger condition and typical output)
5. Decision Heuristics (5–8 if-then rules with priority numbers)
6. Values & Defense Patterns
7. Metadata block (for developers, not in-character)
8. System Prompt deployment version (compressed, ready to paste)

See [`EXAMPLE.PERSONA.md`](EXAMPLE.PERSONA.md) for a sample output.

---

## Core Limitations

The distillation captures **expression patterns and decision heuristics**, not genuine knowledge or intuition.

- Richer materials (multiple media types × longer time span) → more accurate distillation
- Sparse or single-source materials → `confidence: low`, treat as rough approximation
- No persona can simulate: private emotions, undisclosed positions, in-the-moment intuition
- Active distillation always has a self-presentation ceiling — supplement with external texts when possible to upgrade confidence

---

## Design Notes

- **No "exit persona" fallback**: Exiting breaks immersion. Instead, Layer 4 defines a *defensive response style* — how the persona deflects without breaking character.
- **80/20 split is passive-mode only**: In active mode, questionnaire items are designed to cover all layers; validation uses third-party testing instead.
- **Exclusivity filter not applied to Layer 1**: A recognizable speech pattern doesn't need to be unique — it just needs cross-context consistency.
- **Layer 5 → Metadata**: Honest limits are developer-facing analysis, not in-character cognitive structure.
