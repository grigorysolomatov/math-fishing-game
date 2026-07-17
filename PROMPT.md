Build a complete, polished browser game called "Math Fishing" using Svelte 5.

## Game Concept
- Player must first solve a math puzzle to "unlock" fishing
- After solving, they get to fish using a fun interactive mechanic
- Loop repeats with progressively harder math puzzles each round

## Exact Game Flow (per round)
1. **Math Phase**: Show a math equation (e.g. "2 + 2 = ?"). Player types answer and submits. Wrong answer: shake animation + try again. Correct answer: celebration + transition to fishing.
2. **Fishing Phase**: Show a fishing scene. Player clicks a "Cast!" button, then watches a bobber animate in water. After a random 1–3 second wait, the bobber bobs + player must click "HOOK IT!" quickly (within 1.5 seconds) or the fish escapes. If caught: show a fish with a fun name, add to catch log. Then next round.
3. **Progression**: Each round, math gets harder:
   - Rounds 1–3: simple addition/subtraction (numbers 1–10)
   - Rounds 4–6: multiplication, larger numbers (up to 20)
   - Rounds 7–9: two-step problems like "3 × 4 + 2 = ?"
   - Round 10+: mixed algebra-style: "? + 5 = 12" (find the missing number)

## Visual Design
- Dark theme: background #0a0a0a, surfaces #111, borders #222
- Accent color: #7c3aed (violet)
- Fun fish emoji varieties: 🐟 🐠 🐡 🦈 🐙 🦑 🦐 🦞 🐬 🐳
- Show a running "Catch Log" on the side with fish caught so far
- Round counter and score at the top
- Simple CSS animations: bobber bouncing in water (sine wave), fish caught flying up, wrong answer shake

## Tech Requirements
- Single App.svelte file is fine (or split into logical components)
- Svelte 5 with $state, $derived
- No external libraries beyond what's in the scaffold
- Mobile-friendly (touch works for all interactions)
- The fishing "water" area: just a nice CSS-animated blue/teal rectangle with the bobber as an emoji (🎣 or a circle) that bobs
- The "HOOK IT!" button appears at a random moment and must be clicked fast — missed = "The fish got away!" message

## Fish Names (use these randomly when fish is caught)
"Glimmer", "Bubbles", "Captain Fins", "Old Chompy", "The Silver Flash", "Moonbeam", "Tiny Terror", "Magnifico", "Lord Splash", "Zigzag"

## Files to create/modify:
- `src/App.svelte` — main game (replace the default)
- `src/app.css` — game styles (replace the default, use dark theme)
- `index.html` — update title to "Math Fishing Game"

Make it genuinely fun and polished. Add personality — witty messages for correct/wrong answers, excited messages when catching a fish.

After writing all the code, run: `npm run build`
Fix any build errors you encounter, then confirm "BUILD SUCCESSFUL".
