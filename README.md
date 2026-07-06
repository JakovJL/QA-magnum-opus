# Knowledge Base — QA / AQA study vault

Obsidian vault for structured self-study of Manual QA and AQA (Java). Built around the Innowise trainee programme. Content is bilingual: **English is the primary version, Russian is a mirror translation.**

## How the vault is organised

```
Innowise_trainee/        ← the study programme
├── План обучения.md     ← study plan (3 months, progress-tracked)
├── 1 Modul/             ← Manual QA module
│   ├── Eng/  and  Rus/  ← paired notes (Eng primary, Rus mirror)
│   ├── QA manual eng.md / QA manual rus.md   ← Module Index (MOC)
├── 2 Modul/             ← Java + AQA module
│   ├── Eng/  and  Rus/
│   └── AQA Java eng.md / AQA Java rus.md      ← Module Index (MOC)
├── Interview Practice/  ← full mock-interview decks (Eng + Rus)
├── Examples/            ← Example Test Plan / Test Case / Test Strategy
├── Карточки/QA/         ← spaced-repetition flashcards (Obsidian Spaced Repetition)
├── ISTQB Glossary v3.2.md, Pro ISTQB вопросики…  ← reference material
└── Meetings/

Разное/                  ← misc: English Learning Plan, vocabulary, Markdown cheat-sheet
```

## Key ideas

- **Every module has a Map of Content (MOC).** All notes inside a module link only to their MOC in the `**Related notes:**` block — this keeps the Obsidian graph clean.
- **Notes are numbered** (`00`, `01`, …) so Obsidian sorts them in teaching order.
- **Eng first, then Rus.** When you add a topic, write the English note, then mirror the same meaning in Russian.
- **Each note ends with an Interview Questions section** in four parts: Beginner / Intermediate / Advanced / Code — every question answerable from the note body.

## How to add a new note

1. Pick the module folder (`1 Modul/` or `2 Modul/`), then the language subfolder (`Eng/` first).
2. Number the file to slot into the teaching order (e.g. `06 …`).
3. Follow the note format rules — see **[[AGENTS]]** for the full spec (H1 → Table of Contents → Related notes → content; heading rules; callout conventions; entry structure).
4. Add the same note in the other language.
5. Link the new note from its Module Index (MOC).
6. Update the `Vault Structure` tree in **[[AGENTS]]**.

## Conventions for AI assistants and contributors

All workspace instructions — note format, language rules, callout types, workflow rules — live in **[[AGENTS]]**. That file is the single source of truth.
