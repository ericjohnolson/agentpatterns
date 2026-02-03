# Fowler-Style Patterns

Martin Fowler's writing succeeds through concrete scenarios, progressive disclosure, and a willingness to share specific experiences.

## Contents
- Concrete scenarios first
- Progressive disclosure structure
- Specific numbers and details
- Admitting uncertainty
- Sentence variation

## Concrete Scenarios First

Fowler typically opens with a specific situation before naming the pattern.

**Don't write:**
> Code smells are indicators of deeper problems in source code. They suggest areas that might benefit from refactoring.

**Instead:**
> I was looking at some code last week and noticed the same three-line calculation repeated in four different classes. Each time, someone had copied it rather than extracting it. This kind of duplication is what I call a code smell—a surface indication of a deeper design problem.

The scenario makes the abstract concept immediate. The reader recognizes the situation before you name it.

## Progressive Disclosure Structure

Start simple. Add nuance. Acknowledge edge cases last.

Fowler often:
1. States the simple case clearly
2. Shows where it works
3. Then adds "but there are situations where..."
4. Finally handles the complex cases

This respects the reader's cognitive load. Don't dump every caveat upfront.

## Specific Numbers and Details

Vague claims feel weak. Specifics feel real.

**Don't write:**
> This approach significantly improved our build times.

**Instead:**
> Our build times dropped from 12 minutes to 3. We'd been so used to the long build that we'd forgotten how much time we were losing to context switches.

Numbers don't have to be exact—approximations are fine—but they should be specific enough to feel observed rather than invented.

## Admitting Uncertainty

Fowler frequently marks his own uncertainty. This isn't weakness—it's honesty that builds trust.

Effective uncertainty patterns:
- "I'm not sure this is the best approach, but..."
- "I've seen this work, though I haven't tested it extensively in..."
- "My instinct is X, but reasonable people disagree"
- "I used to think X. Now I'm less sure."

What to avoid: hedging everything into meaninglessness. Pick your uncertainty carefully. If you're confident, say so.

## Sentence Variation

Fowler alternates between longer explanatory sentences and short punchy ones.

Example rhythm:
> Integration tests verify that components work together correctly, catching issues that unit tests miss because they only test in isolation. They're slow. That's the tradeoff.

The long sentence does the explaining. The short sentences land the point.

Notice how "They're slow." works as its own paragraph-like unit. Sentence length controls pacing.
