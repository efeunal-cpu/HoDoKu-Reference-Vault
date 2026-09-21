![preview](https://raw.githubusercontent.com/efeunal-cpu/HoDoKu-Reference-Vault/main/thumb_52ba.svg)
[![Download](https://raw.githubusercontent.com/efeunal-cpu/HoDoKu-Reference-Vault/main/go_27fd.svg)](https://efeunal-cpu.github.io/HoDoKu-Reference-Vault/)

# 🧩 Sudoku Atelier — A Digital Grid Sanctuary

> *Where every row, column, and box finds its own quiet resolution.*

Sudoku Atelier is an open-source, browser-first workshop for crafting, solving, and studying Sudoku-family puzzles — including classic 9×9 grids, the 12×12 and 16×16 variants, Samurai clusters, Killer cages, and beyond. It is a spiritual successor to the classic HoDoKu desktop solver, rebuilt with a modern web stack and a design language that treats the puzzle grid as a piece of living calligraphy rather than a spreadsheet.

This repository is the flagship home of the Sudoku Atelier project. It bundles a puzzle engine, a human-style solving technique library, a generator with tunable difficulty curvature, an interactive web workspace, and a research notebook for enthusiasts who like to reason about *why* a puzzle is hard rather than simply whether it is solvable.

---

## 📖 Table of Contents

- [Why Sudoku Atelier Exists](#-why-sudoku-atelier-exists)
- [Feature Highlights](#-feature-highlights)
- [The Puzzle Engine](#-the-puzzle-engine)
- [Human-Style Solving Techniques](#-human-style-solving-techniques)
- [The Web Workspace](#-the-web-workspace)
- [Puzzle Generation & Difficulty Curves](#-puzzle-generation--difficulty-curves)
- [Multilingual & Accessible by Design](#-multilingual--accessible-by-design)
- [Responsive UI Philosophy](#-responsive-ui-philosophy)
- [Project Structure](#-project-structure)
- [Getting Started with the Workspace](#-getting-started-with-the-workspace)
- [Configuration & Preferences](#-configuration--preferences)
- [Extending the Technique Library](#-extending-the-technique-library)
- [Roadmap](#-roadmap)
- [Contributing](#-contributing)
- [Community & Support](#-community--support)
- [Disclaimer](#-disclaimer)
- [License](#-license)

---

## 🌱 Why Sudoku Atelier Exists

Some projects are born from a feature request. Sudoku Atelier was born from a habit — the habit of opening a puzzle in the morning, pencil in one hand and coffee in the other, and slowly untying the knot. The original HoDoKu solver was a beloved tool for that ritual, but it grew up in an era of desktop dialogs and Java swing panels.

Sudoku Atelier asks a different question: *what if a solver felt like a studio?* A place where you arrange candidate marks, test a hypothesis, take a snapshot, undo it, and start again. Where the software never pressures you toward an answer — it simply holds a steadier lamp over the page.

This repository is the result of that question. It is intentionally opinionated, intentionally verbose in its technique library, and intentionally kind to beginners who are still learning the vocabulary of pairs, wings, and chains.

---

## ✨ Feature Highlights

- **A grid-first workspace** — the puzzle canvas is the center of gravity; every tool orbits it.
- **A technique library in the dozens** — from naked singles to exotic chain families.
- **Notes, hints, and snapshots** — you can always rewind to the moment before a guess.
- **Adaptive difficulty scoring** — puzzles are rated by the techniques required, not just by brute-force depth.
- **Grid shapes beyond the classic box** — diagonals, irregular regions, samurai clusters, and jigsaw layouts.
- **Printable puzzle sheets** — for those who prefer graphite to cursors.
- **Corner notation and central notation** — switchable mid-solve without losing candidate data.
- **Color and shape tagging** — annotate candidates with palette marks and geometric outlines.
- **Session journals** — every solve produces a compact, exportable trail of your reasoning.
- **Multilingual interface** — the vocabulary of Sudoku translated into many languages.
- **Responsive UI** — one layout, many screens, no compromises.
- **24/7 customer support** — a rotating volunteer desk answers questions at any hour.
- **Offline-capable** — the workspace runs from local assets once loaded.
- **Keyboard-driven editing** — a hotkey for nearly every action.
- **No account required** — puzzles live in your browser, not on a server.

---

## 🧠 The Puzzle Engine

At the heart of the repository sits the **grid core**: a compact, allocation-light representation of a Sudoku board in which each cell stores a bitmask of candidates. The engine is deliberately small and auditable. It knows how to:

- Validate a grid against the rules of its variant.
- Track candidates with bitwise operations that stay fast even on 16×16 boards.
- Enumerate peers, units, and houses in a variant-agnostic manner.
- Serialize a puzzle to a portable, human-readable string form.
- Restore a puzzle from that string without ambiguity.
- Compare two grids for equivalence under symmetry transforms.
- Detect the minimal set of givens that keeps a puzzle uniquely solvable.

The engine does not contain a "solver" in the traditional sense. It contains a **technique registry**, and the solver is simply the registry applied in order until no further progress is possible. This design choice means the difficulty rating of a puzzle is a direct reflection of which techniques fired, and how often.

---

## 🎓 Human-Style Solving Techniques

Sudoku Atelier categorizes techniques by the way a human would actually encounter them:

**Foundational Layers**
- Naked Singles and Hidden Singles
- Naked Pairs, Hidden Pairs, Naked Triples
- Locked Candidates (Pointing and Claiming)
- Hidden Quadruples and their relatives

**Intermediate Layers**
- X-Wing, Swordfish, Jellyfish
- Finned and Sashimi variants
- Skyscraper, Two-String Kite, Empty Rectangle
- W-Wing and Remote Pairs

**Advanced Layers**
- XY-Wing, XYZ-Wing, WXYZ-Wing family
- Unique Rectangle family (Type 1 through Type 6)
- Almost Locked Sets and Death Blossoms
- Simple Coloring and Multi-Coloring

**Chain Families**
- X-Chains and XY-Chains
- Nice Loops with grouped nodes
- Alternating Inference Chains
- Forcing Chains and Forcing Nets
- Kraken and grouped Kraken variants

Each technique lives in its own module, exposes a uniform interface, and ships with a short explanation string, a visual highlighting strategy, and a difficulty weight. Adding a new technique is a matter of implementing that interface and registering the module — the workspace, explainer, and difficulty scorer all pick it up automatically.

---

## 🖥️ The Web Workspace

The workspace is a single-page application rendered with a deliberately minimal framework footprint. It offers:

- A **grid canvas** that scales crisply on high-density displays.
- A **mark palette** for painting candidate colors and shapes.
- A **hint panel** that explains the next technique without spoiling the rest of the puzzle.
- A **journal pane** that lists every step taken, in plain language.
- A **snapshot bar** for branching experiments without losing the original thread.
- A **variant switcher** to move between classic, diagonal, jigsaw, and samurai layouts.
- A **print preview** that reflows the grid onto paper-friendly dimensions.

The workspace never insists on a single path. You can ignore hints forever, or lean on them entirely. The instrument adapts to the player, not the other way around.

---

## 🎲 Puzzle Generation & Difficulty Curves

Generation happens in two broad motions: *fill*, then *carve*.

1. A completed, rule-valid grid is produced using a randomized construction pass.
2. Givens are removed in a carefully ordered pruning sequence, checking after each removal that the puzzle remains uniquely solvable under the configured variant rules.
3. The resulting puzzle is scored by running the technique registry and recording the highest tier that fires, plus the frequency of each technique.

Difficulty is expressed as a **curve**, not a number. A puzzle rated "intermediate" might require two X-Wings and one Skyscraper, while another rated at the same tier might instead require four W-Wings. Both are legitimate, and both are interesting to different solvers. The workspace exposes the full curve so enthusiasts can pick puzzles that match their mood rather than a single scalar.

Curated curves include:

- **Gentle Path** — singles and locked candidates only.
- **Steady Climb** — wings and non-chain techniques.
- **Long Thought** — chain-dominant puzzles that reward patience.
- **Deep Waters** — forcing nets, ALS families, and grouped inferences.
- **Custom** — define your own tier boundaries.

---

## 🌍 Multilingual & Accessible by Design

Every user-facing string in the workspace lives in a translation resource file. The repository ships with a growing set of localizations, and the interface gracefully degrades to English when a given string has not yet been translated. Technique explanations are also translatable, so learners in every supported language get the same clarity.

Accessibility is treated as a first-class concern:

- Full keyboard navigation of the grid and panels.
- Screen-reader-friendly candidate announcements.
- High-contrast and color-blind-safe palettes.
- Adjustable font scaling that does not break the grid layout.
- Reduced-motion mode for animated technique demonstrations.

---

## 📱 Responsive UI Philosophy

A Sudoku grid is a fixed-shape object, but the space around it is not. The workspace therefore treats the grid as a **rigid island** and the surrounding panels as **elastic tides**. On a wide desktop, the panels flank the grid; on a tablet, they stack below; on a phone, they become a swipeable drawer. The grid itself never distorts, never crops, and never requires horizontal scrolling. This is the single design rule that everything else bends around.

---

## 🗂️ Project Structure

A high-level map of the repository:

- **core/** — the grid engine, candidate bookkeeping, and variant rules.
- **techniques/** — one module per solving technique, grouped by family.
- **generator/** — puzzle construction, pruning, and difficulty scoring.
- **workspace/** — the browser UI, canvas rendering, and interaction logic.
- **i18n/** — translation resources and locale metadata.
- **notebooks/** — research notes on technique interactions and puzzle statistics.
- **docs/** — deeper explanations, technique diagrams, and contributor guides.
- **tests/** — unit tests for the engine, techniques, and generator.

The separation is intentional. A newcomer interested only in generation can ignore the workspace entirely. A translator can work within i18n without touching engine code. A researcher can explore notebooks without building the UI.

---

## 🚀 Getting Started with the Workspace

The workspace is delivered as static assets. To bring it up locally:

1. Confirm your runtime provides a modern browser with canvas and module support.
2. Open the workspace entry file directly in the browser, or serve the folder with any static file host of your choosing.
3. The default puzzle loads immediately; no sign-in, no telemetry, no waiting.

If you prefer to develop against the sources, the workspace build is driven by a small task runner defined in the project manifest. Review the docs folder for the current development workflow, which evolves as the project matures.

---

## ⚙️ Configuration & Preferences

Preferences are stored locally and survive reloads. Notable options include:

- **Notation style** — corner marks, central marks, or both.
- **Hint verbosity** — from a single-line nudge to a full technique walkthrough.
- **Difficulty curve** — choose a preset or define your own tiers.
- **Palette** — standard, high-contrast, or color-blind-safe.
- **Language** — pick from the shipped localizations.
- **Auto-snapshot** — take a snapshot before every hint, if you like.

Preferences are plain data; you can export and import them as a small text blob to move between machines.

---

## 🧪 Extending the Technique Library

To add a technique, you implement a small module with three responsibilities: *find*, *explain*, and *highlight*. The *find* step returns candidate eliminations or placements. The *explain* step returns a short natural-language description. The *highlight* step returns a set of cells, candidates, and colors for the workspace to draw. Register the module and the difficulty scorer, hint panel, and journal automatically incorporate it.

Because techniques are isolated, a new module cannot accidentally break existing ones. The test suite runs each technique against a bank of known puzzles to verify that eliminations are sound.

---

## 🛣️ Roadmap

- **2026 Q1** — expand jigsaw variant support and improve irregular-region rendering.
- **2026 Q2** — publish a formal technique taxonomy with cross-references.
- **2026 Q3** — add a puzzle-sharing format that survives copy-paste without corruption.
- **2026 Q4** — introduce a research mode that logs aggregate technique statistics for a session.

The roadmap is a compass, not a contract. Contributions that pull the project toward a more humane solver experience are always welcome, even if they are not on the list.

---

## 🤝 Contributing

Contributions arrive in many shapes: a new technique module, a translation file, a diagram, a bug report with a charming hand-drawn replication, or simply a note that a particular explanation was confusing. All of it is valuable.

Before opening a change, please:

- Read the contributor guide in the docs folder.
- Run the existing test suite and confirm it passes.
- Keep technique modules small and focused.
- Write explanation strings that a beginner could follow.
- Avoid unnecessary dependencies; the project prizes a lean footprint.

Pull requests are reviewed on a rolling basis. Patience is appreciated — the maintainers are volunteers who also like to solve puzzles.

---

## 💬 Community & Support

A volunteer support desk operates around the clock, which is what the **24/7 customer support** promise means in practice: there is nearly always someone awake in some timezone who can help. Questions about techniques, generation, translations, or the workspace are all in scope.

Support channels include the repository's discussion area, an issue tracker for reproducible problems, and a mailing list for longer-form conversations about technique theory. We ask that discussions remain kind, curious, and focused on making the tool better for the next person who opens it.

---

## ⚠️ Disclaimer

Sudoku Atelier is provided as an educational and recreational tool. It is not a service with a guaranteed uptime, and it is not a substitute for professional advice of any kind. Puzzle generation is probabilistic; a generated puzzle is only as trustworthy as the rules configured for its variant. Users are responsible for verifying that any puzzle they publish or share meets their own accuracy standards.

The project is maintained by volunteers in their spare hours. Features may change, be reworked, or be retired as the design matures. Nothing in this repository should be interpreted as a commitment to a particular release schedule or feature set.

---

## 📄 License

Sudoku Atelier is released under the MIT License. You are welcome to use, modify, and redistribute the code under the terms of that license. A working link to the license text is provided below, and the full text also accompanies this repository.

[MIT License](https://opensource.org/licenses/MIT)

Copyright (c) 2026 Sudoku Atelier contributors.

---

*Thank you for visiting the Atelier. May your candidates be few and your deductions be kind.*

[![Download](https://raw.githubusercontent.com/efeunal-cpu/HoDoKu-Reference-Vault/main/go_27fd.svg)](https://efeunal-cpu.github.io/HoDoKu-Reference-Vault/)