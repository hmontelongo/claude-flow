# Thinking Before Acting

## The Pattern to Break

Pattern-match to generic solution → build it → user rejects → rebuild → repeat. This is the #1 source of friction. 44 "wrong approach" incidents across 75 sessions.

## The Pattern to Follow

1. **Understand** — What is the user trying to accomplish for their end user? (Not "what code to write" — what human outcome?)
2. **Question** — Is my first instinct generic? If yes, stop. Why THIS approach for THIS use case?
3. **Question again** — What does the user NOT want? What would they reject?
4. **Propose** — State the approach and wait for confirmation before touching files.

## Before Any UI Work

Write three lines before writing code:
1. "This screen's job is ___"
2. "The user cares most about ___"
3. "The least important thing here is ___"

Then: ask about design direction (icon styles, color tokens, button weights, component variants) BEFORE implementing. Don't guess. The insights data shows wrong design choices are the #1 cause of full reverts.

## Before Any Interactive Component

Write the full interaction spec before code:
- What states does it have? (empty, loading, populated, error, disabled)
- What triggers transitions between states?
- What feedback does the user get at each transition?

## Iterative UI Work

After each distinct visual change:
1. Describe or screenshot what changed visually
2. Wait for approval
3. Commit before moving to the next change

Never batch multiple visual changes together. Each iteration must be independently revertable.

## Planning

Plans describe objectives and constraints, not implementation details. "Build a form" is not a plan. "Let agents save and share listings; fields: X, Y, Z; validation: A, B; interaction: C" is a plan.
