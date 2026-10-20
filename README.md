# ClaudeMT

ClaudeMT v3 (Claude Minimalist Thinking & Diff-Reviewer) is a planning and review skill for [Claude Code](https://code.claude.com). It is prompt-only: one `SKILL.md`, with no scripts and no hooks. When you ask for a design, the model answers with bullets, diagrams and a decision matrix, and writes no code until you confirm. When you ask for a review, it reads only the diff and reports only real problems. It does not narrate the code or refactor anything outside the diff.

## Rules

| Part | Rule | What the model does |
|---|---|---|
| 1 | No verbal brainstorming | No inner monologue, reasoning traces or pros/cons essays. Each argument is stated once. |
| 1 | No over-engineering | It designs only for the current scope. No microservices, caching layers or abstractions for future needs you did not ask for. |
| 1 | No cascade redesign | When one data structure or interface changes, it adapts only the call sites that change actually breaks. |
| 1 | Zero-code gate | A design or planning request gets a symbolic design only. Code comes after you confirm. |
| 1 | Symbolic format | Bullets of at most 12 words, Mermaid diagrams or pseudocode, and a fixed decision matrix (option, complexity, memory/latency, trade-off) for every architectural choice. |
| 2 | Diff-only scanning | In reviews and refactoring requests it reads only the diff and the changed signatures. It opens or asks for other files only when a breaking interface risk is verified. |
| 2 | Surgical impact mapping | Feedback covers only the modified parts: changed parameters, return types and logic branches. |
| 2 | No narration | It never walks through what the code does. It reports flaws, breaking risks and efficiency issues. |
| 2 | Plausible edge cases only | It skips astronomically unlikely cases and reports practical memory leaks, race conditions, type mismatches and boundary bugs. |
| 2 | Review ≠ refactor | A review never turns into a refactor. Fix suggestions stay inside the diff. |
| 2 | Review format | 🚨 Critical (file:line and a diff fix), ⚠️ Warnings (file:line and a fix of at most 15 words), 🔒 Interface boundary (at most 2 affected call sites). Empty tiers are left out. |
| 3 | Pre-flight checklist | Before every answer it runs 9 checks covering the rules above. |

## How v3 was built

ClaudeMT was drafted with another AI in two versions. v2 rewrote v1 and silently dropped parts of it. v3 is v2 with those parts restored:

- The scope "during code review or refactoring requests" for diff-only scanning, and the ban on requesting untouched files.
- Surgical impact mapping.
- Type insecurities in the warnings tier.
- The rule that every architectural choice uses the decision matrix.
- Two checklist items: no long prose, no scanning of untouched files.
- The purpose lines about full-AST overscanning and zero-code gating.

Where v2 changed a value (bullets went from 15 to 12 words, "Boundary impact" became "Interface boundary"), v3 keeps the v2 value.

The same conversation described three token traps that neither draft had a rule for. v3 adds a rule and a checklist item for each:

- **Each argument once:** models repeat the same argument in different words before they decide.
- **No cascade redesign:** a change to one struct makes the model redesign every component that uses it.
- **Review ≠ refactor:** a review grows into unrequested refactoring of nearby modules.

LaTeX math in the drafts (`$O(1)$`) is written as plain text (`O(1)`), so it reads the same where math is not rendered.

## Install

```bash
git clone https://github.com/omer-faruk-g/ClaudeMT ~/.claude/skills/claudemt
```

Then restart Claude Code. The skill loads when you ask it to design, plan, compare options, review or refactor, or when you type `/claudemt`.

## Limits

- Nothing enforces these rules mechanically. They apply only when the skill is loaded and the model follows them.
- The no-brainstorming rule shapes the visible answer. It cannot reliably switch off the model's hidden extended thinking, which is mainly governed by the effort setting.
- The model cannot measure a 0.01 % probability. Read that threshold as "skip cases that need an unrealistic chain of events".
- Diff-only review can miss bugs in untouched callers. The rule lets the model read other files when the diff changes an interface; ask for a wider review when you need one.
- The zero-code gate adds a round trip by design: after a plan, you have to say "go" before code is written.
- The word limits (12 per bullet, 15 per warning, 2 boundary bullets) can cut detail on large designs.
- No measured improvement yet. The drafting conversation claimed large token savings; those figures were never measured and are left out.

## Background

Sources for the problems ClaudeMT targets:

- **Overthinking:** Chen et al., [Do NOT Think That Much for 2+3=? On the Overthinking of o1-Like LLMs](https://arxiv.org/abs/2412.21187) (2024). Reasoning models spend far more tokens than simple problems need, with little benefit.
- **Small, high-signal context:** Anthropic, [Effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents). It recommends giving an agent the minimum context that carries the most useful signal.
- **Long contexts hurt accuracy:** Liu et al., [Lost in the Middle: How Language Models Use Long Contexts](https://arxiv.org/abs/2307.03172) (2023); Chroma Research, [Context Rot: How Increasing Input Tokens Impacts LLM Performance](https://research.trychroma.com/context-rot) (2025).
- **Plan before code:** Anthropic, [Best practices for Claude Code](https://code.claude.com/docs/en/best-practices), section "Explore first, then plan, then code".

## Türkçe özet

ClaudeMT v3, Claude Code için tek dosyalık bir planlama ve inceleme skill'i:

- Tasarım veya plan isteğinde kod yazmaz. Kısa maddeler, Mermaid diyagramı, sözde kod ve karar matrisiyle cevap verir. Kodu ancak siz onaylayınca yazar.
- Uzun düşünce metni yazmaz, aynı argümanı tekrarlamaz. Yalnızca bugünkü kapsam için tasarlar, "ileride lazım olur" katmanları eklemez.
- Bir veri yapısı değişince yalnızca bozulan çağrı yerlerini uyarlar, tüm mimariyi baştan tasarlamaz.
- İnceleme ve refactor isteklerinde yalnızca diff'i ve değişen imzaları okur.
- Kodun ne yaptığını anlatmaz. Yalnızca gerçek sorunları 🚨 kritik, ⚠️ uyarı ve 🔒 arayüz sınırı başlıklarıyla raporlar.
- İncelemeyi refactor'a çevirmez.

v3, v2'nin v1'den sessizce düşürdüğü kuralları geri getirir. Taslak konuşmasında anlatılan ama hiçbir taslakta kuralı olmayan üç soruna da kural ekler. Taslaktaki tasarruf yüzdeleri ölçülmediği için alınmadı.

Kurmak için yukarıdaki `git clone` komutunu çalıştırın ve Claude Code'u yeniden başlatın.

## License

GPL-3.0-or-later. See [LICENSE](LICENSE).
