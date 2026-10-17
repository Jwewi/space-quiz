# Copilot instructions

## Project overview

This is a dependency-free static web app. The entire quiz lives in `index.html`, including:

- Semantic page structure and accessible button-based answer controls
- All styling in the `<style>` block
- The question bank, quiz state, rendering, scoring, feedback, and results flow in the `<script>` block

There is no package manager, build system, test runner, linter, server, or generated output. The file can be opened directly with a `file://` URL in a browser.

## Build, test, and lint

No build, test, or lint commands are configured. For functional validation, open `index.html` in a browser and exercise the quiz manually or with browser automation:

1. Confirm the initial question, progress indicator, and score render.
2. Activate an incorrect answer and verify the red shake state, correct answer reveal, and unchanged score.
3. Activate a correct answer and verify the green mission-control state, positive feedback, score increment, and disabled answers.
4. Advance through all 10 questions and verify the results screen and restart flow.
5. Check keyboard focus and activation because answers and navigation controls are native `<button>` elements.

When using the integrated browser, load `file:///C:/Users/jaoua/Desktop/space-quiz/index.html` (or the equivalent workspace path) and use the browser's page snapshot/evaluation tools for smoke checks.

## Architecture and data flow

The app is intentionally organized as one self-contained document:

- `questions` is the source of truth for the 10-question quiz. Each item has a `question`, four `answers`, and a zero-based `correct` index.
- `current`, `score`, and `answered` are the only mutable quiz state.
- `renderQuestion()` derives the visible question, progress width, score label, feedback reset, and answer buttons from state.
- `chooseAnswer()` enforces one selection per question, disables all answer buttons, applies `.correct`/`.wrong` visual states, updates feedback, and reveals the next button.
- `showResults()` swaps `.quiz-view` for `.results` and selects the result emoji/message based on the final score.
- The restart handler resets state and calls `renderQuestion()`; it does not reload the document.

Keep DOM IDs and the state/rendering relationships stable when extending the quiz. Dynamic answer buttons are created in JavaScript, so answer-related styling and behavior must support both initial markup assumptions and generated elements.

## Codebase conventions

- Keep changes dependency-free and contained in `index.html` unless a new requirement clearly needs another file.
- Preserve the narrow centered layout and the existing design-token approach in `:root`. Theme-specific overrides belong in the existing `@media (prefers-color-scheme: light)` block.
- Use the existing CSS custom properties for colors and surfaces rather than introducing unrelated hard-coded theme colors. Correct/wrong states should remain visually distinct in both themes.
- Preserve native buttons, `type="button"`, `:focus-visible` outlines, `aria-live`, `role="status"`, and the existing reduced-motion media query.
- Keep quiz content changes in the `questions` array and update the `correct` index whenever answer ordering changes.
- Use the existing class/state vocabulary (`.correct`, `.wrong`, `.good`, `.bad`, `.show`, `.hide`) so visual state and interaction logic remain aligned.
- Maintain the current 10-question flow unless the UI and result copy are deliberately updated together. Progress is calculated from `questions.length`, while the header copy currently says “10 missions”.
- Avoid external fonts, scripts, images, or network calls; the page is expected to work when opened directly from disk.
