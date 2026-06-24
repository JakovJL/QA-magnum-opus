# AGENTS.md — Instructions for working with this Obsidian vault

## Vault Structure

```
knowledge base/
├── Innowise_trainee/
│   ├── План обучения.md
│   ├── Карточки/
│   │   └── QA/
│   │       └── 00 Testing Fundamentals.md
│   ├── 1 Modul/
│   │   ├── Eng/
│   │   │   ├── QA manual eng.md
│   │   │   ├── 00 Testing Fundamentals.md
│   │   │   ├── 01 Types of Testing.md
│   │   │   ├── 02 SDLC and STLC.md
│   │   │   ├── 03 Test Artifacts.md
│   │   │   ├── 04 Test Pyramid.md
│   │   │   ├── 05 Test Estimation.md
│   │   │   ├── 06 Test Design Techniques.md
│   │   │   ├── 07 Bug Report and Defect Life Cycle.md
│   │   │   ├── 08 Working with Requirements.md
│   │   │   ├── 09 API Testing Basics.md
│   │   │   ├── 10 Regression Testing.md
│   │   │   ├── 11 Web Technologies.md
│   │   │   ├── 12 Mobile App Testing.md
│   │   │   ├── 13 Databases.md
│   │   │   ├── 14 Test Management.md
│   │   │   ├── 15 Security Testing Basics.md
│   │   │   ├── 16 Test Automation Fundamentals.md
│   │   │   └── 17 Performance Testing.md
│   │   └── Rus/
│   │       ├── QA manual rus.md
│   │       ├── 00 Основы тестирования.md
│   │       ├── 01 Виды тестирования.md
│   │       ├── 02 SDLC и STLC.md
│   │       ├── 03 Тестовые артефакты.md
│   │       ├── 04 Пирамида тестирования.md
│   │       ├── 05 Эстимация.md
│   │       ├── 06 Техники тест-дизайна.md
│   │       ├── 07 Баг-репорт и жизненный цикл дефекта.md
│   │       ├── 08 Работа с требованиями.md
│   │       ├── 09 Основы тестирования API.md
│   │       ├── 10 Регрессионное тестирование.md
│   │       ├── 11 Веб-технологии.md
│   │       ├── 12 Тестирование мобильных приложений.md
│   │       ├── 13 Базы данных.md
│   │       ├── 14 Управление тестированием.md
│   │       ├── 15 Основы тестирования безопасности.md
│   │       ├── 16 Основы автоматизации тестирования.md
│   │       └── 17 Тестирование производительности.md
│   └── 2 Modul/
│       ├── Eng/
│       │   ├── AQA Java eng.md
│       │   ├── 00 Java Fundamentals.md
│       │   ├── 01 Variables Data Types Operators.md
│       │   ├── 02 Control Flow.md
│       │   ├── 03 OOP Basics.md
│       │   ├── 04 OOP Inheritance Polymorphism.md
│       │   ├── 05 OOP Interfaces Abstraction.md
│       │   ├── 06 Enums.md
│       │   ├── 07 String in Depth.md
│       │   ├── 08 Java Memory Model.md
│       │   ├── 09 Generics.md
│       │   ├── 10 Arrays and Collections.md
│       │   ├── 11 Exception Handling.md
│       │   ├── 12 Lambda, Streams and Optional.md
│       │   ├── 13 Annotations and Reflection Basics.md
│       │   ├── 14 File I-O.md
│       │   ├── 15 Build Tools Maven Gradle.md
│       │   ├── 16 Unit Testing with JUnit 5.md
│       │   ├── 17 Selenium WebDriver Basics.md
│       │   └── Practice - QA Issue Tracker.md
│       └── Rus/
│           ├── AQA Java rus.md
│           ├── 00 Основы Java.md
│           ├── 01 Переменные типы данных операторы.md
│           ├── 02 Управляющие конструкции.md
│           ├── 03 Основы ООП.md
│           ├── 04 ООП Наследование и полиморфизм.md
│           ├── 05 ООП Интерфейсы и абстракция.md
│           ├── 06 Перечисления Enum.md
│           ├── 07 Строки подробно.md
│           ├── 08 Модель памяти Java.md
│           ├── 09 Дженерики.md
│           ├── 10 Массивы и коллекции.md
│           ├── 11 Обработка исключений.md
│           ├── 12 Лямбда, Streams и Optional.md
│           ├── 13 Аннотации и основы Reflection.md
│           ├── 14 Файловый ввод-вывод.md
│           ├── 15 Сборочные инструменты Maven Gradle.md
│           ├── 16 Модульное тестирование с JUnit 5.md
│           ├── 17 Основы Selenium WebDriver.md
│           └── Практика - QA Issue Tracker.md
├── Examples/
│   ├── Example Test Plan.md
│   ├── Example Test Case.md
│   └── Example Test Strategy.md
├── templates/
├── Дневник/
└── Разное/
    ├── English Learning Plan.md
```

## File Conventions

- New modules follow the pattern: `N Modul/` inside `Innowise_trainee/`
- Each module has `Eng/` and `Rus/` subfolders
- Files are numbered: `00`, `01`, `02` for correct Obsidian sort order
- English is the primary version — Russian is a translation
- When a new topic is added to Eng, add the same to Rus
- Edit English first, then mirror to Russian. The two versions must convey the same meaning.

## Note Format Rules

- Every note starts with `# Title` (H1)
- Followed by `## Table of Contents` with Obsidian `[[#Section]]` links
- Followed by `**Related notes:**` block with a link to the Module Index (e.g. `[[QA manual eng]]`)
- Then `---` divider, then content
- Headings: H1 → H2 → H3, no skipped levels
- No **bold** inside headings
- Title Case for headings in **English** notes; Russian notes use normal sentence capitalisation
- No numbered headings — except exercise/task lists (e.g. `### 1. Reverse Array` in Practical Tasks)

### Section Separators

- Every `##` section must be preceded by `---` — except `## Table of Contents`, which follows the H1 directly
- `---` between `###` subsections only when they are long and self-contained (e.g. each subsection has its own Definition / Advantages / Disadvantages / Example block)
- No `---` between short `###` subsections that are just a heading + a few lines

## Language Rules

- Notes are in English (Eng/) or Russian (Rus/)
- Every file must be fully in one language — no mixing within a file
- Callout titles, body text, and labels must match the file language
- Technical terms stay in English in both versions: Smoke, Sanity, Ad-hoc, CI, UAT, API, ARIA, SDLC, STLC (and library-specific ones like HashMap, ArrayList)
- Terms with both names: Error (Ошибка), Defect (Дефект), Failure (Отказ)
- User's English level: B1 — keep vocabulary simple, avoid rare words

## Entry Structure

When a `###` subsection describes a specific concept, type, or approach, use this order:

1. **Definition** — 1–2 sentences. What it is.
2. **Goal:** — what it verifies or achieves.
3. **How it works:** — internal mechanism, when relevant (e.g. HashMap buckets, ArrayList growth). Optional.
4. **Advantages:** — bullet list.
5. **Disadvantages:** — bullet list.
6. **Example:** — concrete, short. Optional but preferred.
7. **When to use:** — optional, only when the choice is non-obvious.

Not every field is required for every entry. Short entries (a single bullet in a list) skip structure and use one sentence only.

When a section contains multiple entries of equal depth, use bullet list format (not `###` subsections) unless each entry needs Advantages/Disadvantages/Example blocks.

---

## Note Logic

Each note covers one topic completely. The topic is broken into **classification axes** — different ways to look at the same subject.

- Each classification axis = one `##` section
- Axes are independent: a reader can read any section without needing the others
- Within a section, items are listed either as bullets (short) or `###` subsections (rich)
- If two concepts are commonly confused, add a comparison table in `[!tip]` at the end of the section
- If a concept in one section is fully covered in another section or file, add a cross-reference instead of repeating content

**Example for a Types of Testing note:**
- `##` By goal → Functional / Non-functional
- `##` By access to code → Black / White / Grey box
- `##` By change relation → Regression / Retesting
- `##` By execution → Manual / Automated
- `##` By architecture level → Unit / Integration / System / Acceptance

Each axis answers a different question about the same subject. Together they give a complete picture.

### Interview Questions Must Be Backed by the Body

Many notes end with an "Interview Questions" or self-check section. Every question there must be answerable from the note body — the body teaches the concept first, the questions only test recall.

- If a question references a topic that is **not** taught in the body, that is a gap: either teach the topic in the body, or remove the question.
- A topic that appears **only** as a Q&A pair (never explained) violates this rule.
- When editing, cross-check: take the question list, confirm each topic exists in the body (e.g. by searching for the key term). If a term is missing, add the section.
- Scope: applies when the task involves questions or note coverage. For a one-off fix (e.g. a typo), Workflow Rule #3 (Surgical Changes) takes precedence — do not expand scope.

---

## Workflow Rules (from user)

### 1. Think Before Coding
- State assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them — don't pick silently.
- If a simpler approach exists, say so.

### 2. Simplicity First
- Minimum changes that solve the problem.
- No features beyond what was asked.

### 3. Surgical Changes
- Touch only what you must.
- Don't improve adjacent content that wasn't asked about.
- Match existing style.

### 4. Propose Before Applying
- Always show proposed changes first.
- Apply only after explicit approval.
- Show before/after when applying changes.

### 5. Table of Contents Check
- After **every** edit to a note, verify that the Table of Contents matches the current headings.
- If a section was added, renamed, or removed — update the TOC in the same edit.
- TOC entries use Obsidian link syntax: `[[#Section Name]]`
- Indented entries (`\t`) reflect `###` subsections under their parent `##`.
- List in the TOC every `###` that names a concept, type, or comparison (e.g. `ArrayList`, `Ordered vs Sorted`). Omit `###` that are just structural parts of an entry (`Key Rules`, `Advantages`, `Example`).

### 6. Vault Structure Update
- After creating a **new** note (both Eng and Rus versions), update the `Vault Structure` section in this `AGENTS.md` file.
- Ensure the file tree stays visually consistent.

### 7. Module Index (MOC)
- Every module must have a central Index file (e.g., `QA manual eng.md`).
- This file acts as a Map of Content (MOC) to keep the Obsidian Graph clean.
- All notes within the module must link **only** to this Index in their `**Related notes:**` block.
- Semantic links are allowed within the text body if they add conceptual value.

## Content Standards

- Tables use standard Markdown table syntax
- Cross-links between related files use `[[filename]]` syntax (no .md extension)
- Intra-note cross-references use `→ see [[#Section Name]]` syntax
- Code blocks must declare their language — ```java by default; ```sql / ```bash for non-Java

### Reference Material Lives With Its Topic

Methods, properties, and API tables belong **inline** in the section that introduces the type or concept — not in a separate "cheat sheet" or "summary" section.

- A `List` methods table goes inside the `List` section, next to `ArrayList`/`LinkedList`.
- A `Map` methods table goes inside the `Map` section.
- Do **not** create a standalone "Key Methods Cheat Sheet" that dumps methods for many types together — it duplicates content and drifts out of sync.
- Exception: utility classes (`Arrays`, `Collections`) that operate on collections **from the outside** get their own dedicated section, because they are a different category (external static helpers, not instance methods).

### Callout Conventions

Callouts use Obsidian syntax: `> [!type] Title`

| Type | When to use |
|---|---|
| `[!tip]` | Comparison tables, best practices, positive guidance |
| `[!info]` | Neutral clarification: definitions, facts, how X and Y relate |
| `[!warning]` | Attention: an order, sequence, or hierarchy that must be respected |
| `[!danger]` | The single core, must-not-miss rule or critical definition |
| `[!caution]` | Common mistakes, pitfalls, edge cases, what NOT to do |

- Comparison tables always go inside a `[!tip]` callout at the end of the relevant section
- Callout title should name what is being shown: `Quick comparison`, `Regression vs Retesting`, `Alpha vs Beta Testing`

### Interview Questions Section Structure

Notes with an Interview Questions section use four fixed parts:

1. **Beginner Questions** / Вопросы начального уровня — basic recall
2. **Intermediate Questions** / Вопросы среднего уровня — understanding and judgement
3. **Advanced Questions** / Вопросы повышенного уровня — deep / internals
4. **Code Questions** / Вопросы с кодом — show code, ask what it prints / whether it is correct and why; then explain.

Every question must be backed by the body (see *Interview Questions Must Be Backed by the Body*).
