# Claude Science : System Prompt & Skills

A collection of system prompts, skills, and runtime contracts for **Claude Science**, Anthropic's official research workbench for Claude.

This repository contains:

- **`prompt/`** : Production system prompts for Claude Science (Opus 5, Fable 5), standard Claude system prompts, and modular fragments.
- **`skills/`** : Reusable agent skills extending research and data analysis workflows.
- **`runtime/`** : Injection contracts and tool surface specifications defining how the harness interacts with the model.

---

You might be here for these system prompts : 
* [Claude Science Fable 5](https://github.com/Shoko-official/Claude-Science-System-Prompts/blob/main/prompt/fragments/claude-science/system-prompts/claude-science-fable-5-system-prompt.md)
* [Claude Science Opus 5](https://github.com/Shoko-official/Claude-Science-System-Prompts/blob/main/prompt/fragments/claude-science/system-prompts/claude-science-opus-5-system-prompt.md)

## System Architecture & Comparisons

### 1. Standard Claude vs. Claude Science

Standard Claude operates as a general-purpose chat assistant. **Claude Science** is a managed research workbench that executes inside a strict permission and environment harness.

```mermaid
flowchart TD
    subgraph Standard["Standard Claude (General Assistant)"]
        direction TB
        U1[User] -->|Chat message| C1[Claude Model]
        C1 -->|System Prompt: General Product Info, Tone, Safety, Memory, General Tools| C1
        C1 -->|Response / Artifact / Web Search| U1
    end

    subgraph Science["Claude Science Workbench"]
        direction TB
        U2[User] -->|Research Task / Annotation / Skill| H[Claude Science Harness]
        H -->|Injected System Prompt + Session Context + Granted Folder Scopes| C2[Claude Model]
        C2 -->|Tool Request| P{Permission Card}
        P -->|Approved| T[Managed Tool Surface]
        P -->|Denied| C2
        
        T --> K[Persistent Python / R Kernels]
        T --> A[Artifact Store & Provenance Log]
        T --> R[Remote Compute: SSH / SLURM / Cloud]
        T --> S[Specialist Agents & Reviewer]
        
        K -->|Execution Log| C2
        A -->|Versioned Output| C2
        S -->|Subagent Findings| C2
        
        C2 -->|GFM Response + Deliverables| U2
    end
```

#### Key Differences Table

| Feature | Standard Claude (`system-prompts/opus-5-...`) | Claude Science (`prompt/fragments/claude-science/system-prompts/...`) |
|---|---|---|
| **Primary Framing** | General AI assistant, creative partner, web/office product integrations | Interactive research workbench for scientific investigation & computational analysis |
| **Execution Model** | Standard chat / lightweight code execution | Persistent Python/R kernels, local accelerators, SSH/SLURM/cloud compute jobs |
| **Permissions** | Feature toggles (search, artifacts, memory) | Explicit permission boundaries per folder, connector, host, and compute job |
| **Artifacts** | UI widgets, documents, code snippets | Durable versioned project artifacts with full execution log & review provenance |
| **Safety Scope** | General safety stance, child safety, copyright, standard guardrails | Global dual-use safety instructions (biological, chemical, radiological, nuclear, cyber) |

---

### 2. Claude Science Execution & Provenance Flow

```mermaid
sequenceDiagram
    actor Researcher as User / Researcher
    participant Harness as Workbench Harness
    participant Model as Claude (Opus 5 / Fable 5)
    participant Kernel as Python/R Persistent Kernel
    participant Subagent as Specialist Agent
    participant Storage as Artifact & Scratchpad Store

    Researcher->>Harness: Submit research query / task
    Harness->>Model: Inject System Prompt + Environment State + Permissions
    
    alt Non-trivial task start (Fable 5)
        Model-->>Researcher: 1-sentence action summary before tool execution
    end

    Model->>Storage: Create scratch files in <scratchpad-dir>
    Model->>Kernel: Run computational analysis (Python / R)
    Kernel-->>Model: Execution Log + Outputs

    opt Parallel Sub-investigation
        Model->>Subagent: Delegate bounded task (Agent tool)
        Subagent-->>Model: Subagent report
    end

    Model->>Storage: Save durable output via Artifact tool
    Storage-->>Model: Return versioned artifact link & metadata

    Model-->>Researcher: Final GFM response (Outcome first -> Supporting evidence -> Artifact link)
```

---

## Directory Structure

```
prompt/
├── system-prompts/                 ← Full standard Claude system prompts
│   ├── fable-5-system-prompt.md
│   └── opus-5-system-prompt.md
└── fragments/
    ├── claude-science/              ← Claude Science modular fragments & compiled prompts
    │   ├── common-before-behavior.md
    │   ├── opus-behavior.md
    │   ├── fable-behavior.md
    │   ├── common-after-behavior.md
    │   ├── tools.md
    │   └── system-prompts/
    │       ├── claude-science-fable-5-system-prompt.md
    │       └── claude-science-opus-5-system-prompt.md
    └── generic/                     ← Generic system injections & slash commands
        ├── system-reminders/        (btw, container-restart, model-switched, non-interactive...)
        ├── slash-commands/          (brief-mode, insights, recap, session-title...)
        ├── compact/                 (context compression & continuation)
        └── tool-definitions/        (Glob, Grep definitions)

skills/
├── conducting-scientific-research/  ← Scientific research methodology skill
└── kaggle-competition/              ← Kaggle competition skill (git-excluded)

runtime/
├── injection-contract.md           ← Harness injection specification
└── proposed-tool-surface.md         ← Full tool surface & permission boundary definitions
```

---

## Repository & Licensing

**Original Repository:** https://github.com/Shoko-official/Claude-Science-System-Prompt  
**Author / Original Creator:** [Shoko-official](https://github.com/Shoko-official)

Licensed under [Creative Commons Attribution 4.0 International (CC BY 4.0)](./LICENSE). You are free to share and adapt this material for any purpose, provided appropriate credit is given to **Shoko-official** with a link to the original repository.
