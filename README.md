# Cursor Model Decision Tree

This document serves as a strategic guide for selecting the most effective AI models within Cursor based on the nature of the task. By leveraging the "Model Arbitrage" strategy, we optimize for cost and capability.

*Last Updated: January 2026 (Cursor v2.3)*

## 💰 The "Why": 2026 Model Pricing Context

We switch models because the price difference is massive (up to 100x).

| Role             | Model                 | Cost (Input/Output)  | Best Use Case                                |
| :--------------- | :-------------------- | :------------------- | :------------------------------------------- |
| **Engineer**     | **Claude Opus 4.5**   | $$$ ($5.00 / $25.00) | "Break Glass" only. Complex bugs & security. |
| **Senior**       | **Claude Sonnet 4.5** | $$ ($3.00 / $15.00)  | Production logic & complex state.            |
| **Architect**    | **GPT-5.2**           | $$ ($1.75 / $14.00)  | Planning & Architecture.                     |
| **Builder**      | **Composer 1**        | $ ($1.25 / $10.00)   | Multi-file editing (Agent mode).             |
| **Auditor**      | **Gemini 3 Pro**      | $$ ($2.00 / $12.00)  | 2M Context Window (Repo-wide reviews).       |
| **Intern/Daily** | **Gemini 3 Flash**    | $ ($0.50 / $3.00)    | Bulk tests, boilerplate, and fast fixes.     |

---

## The 3 "Golden Paths"

If the tree looks complex, just memorize these three common paths.

### 1. The "New Feature" Path (Full Stack)
* **Plan:** `GPT-5.2` creates `plan.md`.
* **Build:** `Composer 1` creates the file structure and skeleton code.
* **Refine:** `Claude Sonnet 4.5` fills in the hard logic (auth, database).
* **Tests:** `Gemini 3 Flash` generates the test suite.

### 2. The "Pixel Perfect" Path (Frontend)
* **See:** Paste screenshot into `Gemini 3 Pro`. Prompt: "Clone this using Tailwind."
* **Tweak:** Use `Gemini 3 Flash` to nudge pixels: "Move that button 2px right."
* **Logic:** Switch to `Sonnet 4.5` only if you need to wire up the API connection.

### 3. The "Legacy Rescue" Path (Refactoring)
* **Analyze:** `Gemini 3 Pro` reads the whole folder (2M context). Prompt: "Explain how this legacy auth flow works."
* **Refactor:** `Claude Opus 4.5` (The big gun). Prompt: "Rewrite this strictly to match the new pattern. Do not break existing users."
* **Verify:** `Gemini 3 Flash` writes regression tests to ensure safety.

---

## Decision Tree Diagram

```mermaid
graph LR
    %% ==========================================
    %% 1. STYLES & DEFINITIONS
    %% ==========================================
    classDef decision fill:#fff9c4,stroke:#fbc02d,stroke-width:2px,rx:5,ry:5,color:#000
    classDef persona fill:#e1f5fe,stroke:#0288d1,stroke-width:2px,rx:10,ry:10,color:#000
    classDef expert fill:#ffccbc,stroke:#d84315,stroke-width:2px,rx:10,ry:10,color:#000
    classDef intern fill:#f3e5f5,stroke:#8e24aa,stroke-width:2px,rx:10,ry:10,color:#000
    classDef finish fill:#c8e6c9,stroke:#388e3c,stroke-width:2px,rx:20,ry:20,color:#000

    Start([🚀 Start]) --> Q1{Task Type?}

    %% ==========================================
    %% 2. FRONTEND WORKFLOW (Top Lane)
    %% ==========================================
    subgraph UI_Flow ["🎨 Frontend & Visuals"]
        direction TB
        UI_Input{Input Source?}
        Designer[Gemini 3 Pro<br><b>Designer</b>]
        UI_Build[Generated Component]
        UI_Review{Tweaks?}
        UI_Logic{State?}
        Styler[Gemini 3 Flash<br><b>Fast Fixer</b>]
    end

    %% ==========================================
    %% 3. BACKEND WORKFLOW (Middle Lane)
    %% ==========================================
    subgraph Backend_Flow ["⚙️ Logic & Architecture"]
        direction TB
        Logic_Stage{Stage?}
        DBA{Risk Level?}
        Architect[GPT-5.2<br><b>Architect</b>]
        Implementation{Reasoning <br> vs Sprawl?}
    end

    %% ==========================================
    %% 4. THE AI TEAM (Shared Resources)
    %% ==========================================
    %% These nodes are shared, so they sit between flows
    Composer[Composer 1<br><b>Builder</b>]
    Senior[Claude Sonnet 4.5<br><b>Senior Logic</b>]
    Daily[Gemini 3 Flash<br><b>Daily Driver</b>]
    Engineer[Claude Opus 4.5<br><b>Expert</b>]

    %% ==========================================
    %% 5. QA & REVIEW (End Lane)
    %% ==========================================
    subgraph QA_Flow ["✅ QA & Review"]
        direction TB
        GruntWork{Tests/Docs?}
        Intern[Gemini 3 Flash<br><b>Intern</b>]
        Review{Final Check}
        Critique[Gemini 3 Pro<br><b>Auditor</b>]
        Deploy([✅ Deploy])
    end

    %% ==========================================
    %% 6. CONNECTIONS
    %% ==========================================

    %% Main Split
    Q1 -- "UI & Frontend" --> UI_Input
    Q1 -- "Logic & Backend" --> Logic_Stage

    %% UI Branch
    UI_Input -- "Screenshot" --> Designer
    Designer --> UI_Build
    UI_Build --> UI_Review
    UI_Review -- "Tweaks" --> Styler
    Styler --> UI_Review
    UI_Review -- "Looks Good" --> GruntWork

    UI_Input -- "New Feature" --> UI_Logic
    UI_Logic -- "Complex State" --> Senior
    UI_Logic -- "Standard" --> Composer

    %% Backend Branch
    Logic_Stage -- "Planning" --> Architect
    Architect --> Implementation
    Logic_Stage -- "Coding" --> Implementation
    Logic_Stage -- "Database" --> DBA

    %% Implementation Choices
    Implementation -- "Many Files / <br> Standard Pattern" --> Composer
    Implementation -- "Heavy Logic / <br> Complex Algo" --> Senior
    Implementation -- "Simple Fix / <br> One Function" --> Daily

    %% Database Choices
    DBA -- "New Table" --> Senior
    DBA -- "Destructive" --> Engineer

    %% Convergence to Grunt Work
    Composer --> GruntWork
    Senior --> GruntWork
    Daily --> GruntWork

    %% Grunt Work Decision
    GruntWork -- "Yes" --> Intern
    GruntWork -- "No" --> Review
    Intern --> Review

    %% Final Review Loop
    Review -- "Audit Context" --> Critique
    Review -- "Bug/Stuck" --> Engineer

    %% Database Bypass (Safety)
    Senior -.-> |"Migration File"| Review
    Engineer -.-> |"Safe Script"| Review

    %% Finish
    Critique --> Deploy
    Engineer --> Deploy

    %% ==========================================
    %% 7. APPLY STYLES
    %% ==========================================
    %% Decisions (Yellow)
    class Q1,UI_Input,UI_Review,UI_Logic,Logic_Stage,DBA,Implementation,GruntWork,Review decision

    %% Premium Models (Blue) - "The Brains"
    class Designer,Architect,Composer,Senior,Critique persona

    %% Expert/High Risk (Red) - "The Safety Net"
    class Engineer expert

    %% Budget/Fast Models (Purple) - "The Grunt Work"
    class Intern,Styler,Daily intern

    %% Start/End (Green)
    class Start,Deploy finish
