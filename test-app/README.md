# Quiz CLI

> An interactive command-line quiz game for testing and reinforcing programming knowledge — built with pure Node.js, zero external dependencies.

[![Node.js](https://img.shields.io/badge/node-%3E%3D18.0.0-brightgreen)](https://nodejs.org/)
[![License](https://img.shields.io/badge/license-MIT-blue)](#license)
[![ES Modules](https://img.shields.io/badge/modules-ESM-yellow)](#)
[![Version](https://img.shields.io/badge/version-1.0.0-informational)](#)

---

## Overview

**Quiz CLI** is a terminal-based quiz game written entirely in Node.js using native built-in modules — no `npm install` required. Players choose a quiz category, select the number of questions, and answer multiple-choice questions directly in the terminal. At the end of each round, a detailed score summary and answer review are displayed.

### Key Features

- 🗂 **Multi-category quiz engine** — JavaScript Basics, Node.js Fundamentals, and General Programming
- 🔀 **Randomized question order** — Questions are shuffled using the Fisher-Yates algorithm on every run
- 📊 **Live progress bar** — Visual ASCII progress indicator rendered between questions
- 💡 **Answer explanations** — Each question ships with a contextual learning explanation shown after answering
- 📝 **Post-quiz review** — Incorrect answers are listed with correct answers after each session
- 🎨 **ANSI terminal colors** — Rich colored output via zero-dependency ANSI escape code utilities
- ♻️ **Replay loop** — Players can replay immediately without restarting the process

### Architecture Overview

The project follows a clean separation of concerns across three core modules:

| Module | Responsibility |
|---|---|
| `index.js` | Application entry point — orchestrates game loop, loads questions, manages session lifecycle |
| `src/quiz.js` | `Quiz` class — encapsulates game state, scoring, progress tracking, and result rendering |
| `src/input.js` | I/O abstraction layer — wraps `node:readline` into Promise-based helpers (`select`, `confirm`, `prompt`) |
| `src/colors.js` | Terminal styling — ANSI escape code utilities with semantic color aliases |
| `data/questions.json` | Static question bank — structured JSON with categories, options, answers, and explanations |

---

## Prerequisites & Installation

### Requirements

- **Node.js** `>= 18.0.0` — uses native `node:fs/promises`, `node:readline`, and ES Module syntax
- **npm** `>= 8.0.0` — bundled with Node.js 18+
- No external packages — `dependencies` field is intentionally absent from `package.json`

### Setup

1. **Clone the repository:**
   ```bash
   git clone https://github.com/priyanka2861/test_ag.git
   cd test_ag/test-app
   ```

2. **Verify your Node.js version:**
   ```bash
   node --version
   # Expected: v18.x.x or higher
   ```

3. **No installation step needed** — the project has zero runtime dependencies. You are ready to run.

---

## Running the Application

### Start the Quiz

```bash
npm start
```

Or invoke directly with Node.js:

```bash
node index.js
```

### Run Built-in Tests

```bash
npm test
```

This executes Node.js's native test runner (`node --test`). Place test files using the `*.test.js` naming convention in the project root or a `tests/` directory.

---

## Usage Walkthrough

Once started, the application guides you through a fully interactive session:

```
  ╔═══════════════════════════════════════════╗
  ║                                           ║
  ║   📚 QUIZ CLI                             ║
  ║   Test your programming knowledge!        ║
  ║                                           ║
  ╚═══════════════════════════════════════════╝

Choose a category:

  1. JavaScript Basics
  2. Node.js Fundamentals
  3. General Programming

Your choice (enter number): 1

How many questions?

  1. All questions
  2. 3 questions
  3. 5 questions

Your choice (enter number): 2
```

**During a question:**

```
[████████░░░░░░░░░░░░░░░░░░░░░░] 20%
Question 1 of 3

What does '===' check for?

  1. Value only
  2. Type only
  3. Value and type
  4. Reference

Your choice (enter number): 3

✓ Correct!
💡 The strict equality operator (===) checks both value and type without coercion.
```

**End-of-round results:**

```
══════════════════════════════════════════════════
  📊 QUIZ RESULTS
══════════════════════════════════════════════════

  Category: JavaScript Basics
  Score: 2/3 (67%)

  👍 Good effort! Keep learning!

══════════════════════════════════════════════════

📝 Review these questions:

1. What is the output of: typeof null?
   Your answer: 'null'
   Correct: 'object'

Would you like to play again? (y/n):
```

---

## Project Structure

```
test-app/
├── data/
│   └── questions.json      # Question bank — categories, options, answers & explanations
├── src/
│   ├── colors.js           # ANSI escape code helpers for terminal color output
│   ├── input.js            # Promise-based readline wrappers (select, confirm, prompt)
│   └── quiz.js             # Quiz class — game state, scoring, progress, results
├── index.js                # Application entry point and main game loop
└── package.json            # Project manifest — scripts, engine requirements, metadata
```

---

## Module Reference

### `src/quiz.js` — `Quiz` Class

```js
import { Quiz } from './src/quiz.js';

const quiz = new Quiz(questions, categoryName);
```

| Member | Type | Description |
|---|---|---|
| `currentQuestion` | getter → `Object \| null` | Returns the active question object |
| `totalQuestions` | getter → `number` | Total count of questions in the session |
| `isComplete` | getter → `boolean` | `true` when all questions have been answered |
| `progress` | getter → `number` | Completion percentage (0–100) |
| `askQuestion(rl)` | async method → `boolean` | Displays question, captures answer, shows feedback |
| `renderProgressBar()` | method → `string` | Returns an ASCII progress bar string |
| `showResults()` | method → `void` | Prints final score summary and incorrect answer review |

**Question shuffle:** The constructor automatically shuffles the provided `questions` array using a Fisher-Yates implementation, ensuring a unique experience on every play.

---

### `src/input.js` — I/O Helpers

```js
import { createInterface, select, confirm, prompt, pressEnter } from './src/input.js';
```

| Function | Signature | Description |
|---|---|---|
| `createInterface()` | `() → readline.Interface` | Creates and returns a stdin/stdout readline interface |
| `prompt(rl, question)` | `(rl, string) → Promise<string>` | Displays a prompt and resolves with trimmed user input |
| `select(rl, question, options)` | `(rl, string, string[]) → Promise<{index, value}>` | Numbered option menu; loops until valid input is received |
| `confirm(rl, question)` | `(rl, string) → Promise<boolean>` | Yes/no prompt; resolves `true` if input starts with `y` |
| `pressEnter(rl, message?)` | `(rl, string?) → Promise<void>` | Pauses execution until the user presses Enter |

---

### `src/colors.js` — Terminal Styling

```js
import * as colors from './src/colors.js';

console.log(colors.success('All tests passed!'));
console.log(colors.error('Something went wrong.'));
console.log(colors.cyan('Informational message'));
```

| Export | Style Applied | Typical Use |
|---|---|---|
| `red(text)` | Red foreground | Raw red text |
| `green(text)` | Green foreground | Raw green text |
| `yellow(text)` | Yellow foreground | Raw yellow text |
| `cyan(text)` | Cyan foreground | Raw cyan text |
| `blue(text)` | Blue foreground | Raw blue text |
| `magenta(text)` | Magenta foreground | Raw magenta text |
| `bold(text)` | Bold | Emphasis |
| `dim(text)` | Dimmed | Secondary / metadata text |
| `success(text)` | Green + Bold | Correct answers, confirmations |
| `error(text)` | Red + Bold | Wrong answers, fatal errors |
| `warning(text)` | Yellow | Caution messages |
| `info(text)` | Cyan | Informational output |
| `highlight(text)` | Magenta + Bold | Headers, titles |

The `colorize(text, ...styles)` base function is also exported for composing custom multi-style strings.

---

## Question Bank Schema

Questions are stored in `data/questions.json` and follow this schema:

```json
{
  "categories": {
    "<category_id>": {
      "name": "Display Name",
      "questions": [
        {
          "question": "The question text?",
          "options": ["Option A", "Option B", "Option C", "Option D"],
          "answer": 0,
          "explanation": "Why this answer is correct."
        }
      ]
    }
  }
}
```

| Field | Type | Description |
|---|---|---|
| `question` | `string` | The question text displayed to the user |
| `options` | `string[]` | Array of answer choices (typically 4) |
| `answer` | `number` | Zero-based index of the correct option in `options` |
| `explanation` | `string` | Educational explanation shown after the user answers |

### Adding a New Category

1. Open `data/questions.json`
2. Add a new key under `"categories"` with a unique ID
3. Provide a `"name"` string and a `"questions"` array following the schema above
4. No code changes required — the application dynamically reads all category keys at startup

**Example:**

```json
"git": {
  "name": "Git & Version Control",
  "questions": [
    {
      "question": "Which command creates a new branch and switches to it?",
      "options": ["git branch new-branch", "git checkout -b new-branch", "git switch new-branch", "git create new-branch"],
      "answer": 1,
      "explanation": "'git checkout -b' creates a new branch and immediately checks it out."
    }
  ]
}
```

---

## Available Scripts

| Script | Command | Description |
|---|---|---|
| `start` | `npm start` | Launches the interactive quiz game |
| `test` | `npm test` | Runs the Node.js native test runner |

---

## JavaScript Concepts Demonstrated

This project is intentionally crafted as a learning reference. The codebase illustrates:

- **ES Modules** (`import`/`export`) with `"type": "module"` in `package.json`
- **Async/await & Promises** — all user I/O is Promise-wrapped for clean async flow
- **Classes & OOP** — `Quiz` class with getters, instance state, and methods
- **Fisher-Yates shuffle** — in-place array randomization algorithm
- **Destructuring** — array and object destructuring throughout
- **Template literals** — used for all dynamic string construction
- **Array methods** — `map`, `filter`, `forEach`, `find`, `slice`
- **Node.js built-ins** — `node:fs/promises`, `node:readline`, `node:path`, `node:url`
- **ANSI escape codes** — manual terminal color rendering without third-party libs
- **Error handling** — `try/catch/finally` in the main loop with graceful `rl.close()`

---

## License

This project is licensed under the **MIT License**. See the [LICENSE](../LICENSE) file for details.
