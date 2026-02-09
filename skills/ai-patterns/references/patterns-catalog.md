# AI Patterns Catalog

Detailed reference for patterns, anti-patterns, and obstacles in AI-augmented software development.

**Attribution:** Patterns by Lada Kesseler (lexler), Llewellyn Falco, Ivett Ördög, and Nitsan Avni
**Source:** https://lexler.github.io/augmented-coding-patterns/

---

## Context Management

Managing AI context, knowledge, and focus.

### Obstacles

#### context-rot

Earlier instructions lose influence as the conversation grows longer. The AI's attention becomes diluted by accumulated context, causing it to drift away from initial directives. This applies when you notice the AI forgetting or ignoring instructions you gave at the start of a session. Address it by resetting context, repeating critical instructions, or using reminders and ground-rules patterns.

#### cannot-learn

LLMs can't learn from interactions within a session because their weights are fixed at training time. They appear to adapt during conversation but actually can't retain information between sessions. This applies whenever you expect the AI to remember corrections or preferences from previous sessions. Address it by externalizing knowledge into files using knowledge-document and extract-knowledge patterns.

#### limited-context-window

The AI has a fixed maximum context size, forcing you to choose what information to keep loaded. Too much context means important details get truncated or forgotten. This applies when working with large codebases or long conversations. Address it by treating context as a scarce resource using context-management, reference-docs, and focused-agent patterns.

#### limited-focus

Too much context causes the AI's attention to become diluted or misdirected, even when it fits within the context window. The AI struggles to prioritize what's important when overwhelmed with information. This applies when you load many files or provide extensive background and notice degraded performance. Address it using focused-agent, semantic-zoom, and reference-docs patterns to limit what's active at once.

#### excess-verbosity

AI systems default to verbose output with low signal-to-noise ratio, producing lengthy explanations when concise answers would suffice. This wastes context space and makes it harder to find key information. This applies to most AI interactions by default. Address it using the noise-cancellation pattern to explicitly request succinct responses.

### Anti-patterns

#### distracted-agent

Using one agent for everything spreads its attention thin, causing instructions to be inconsistently followed. The agent tries to juggle multiple responsibilities and contexts simultaneously, degrading performance on all tasks. This anti-pattern appears when you give a single AI session too many different types of work. Avoid it by using the focused-agent pattern to create specialized agents with narrow responsibilities.

### Patterns

#### context-management

Treat context as a scarce resource requiring active management through deliberate append/reset operations. Monitor what's loaded, remove unnecessary information, and strategically decide when to start fresh versus continuing a session. Use this pattern constantly when working with AI, especially in long sessions or complex projects. Apply it by regularly checking what's in context and resetting when the conversation loses focus.

#### knowledge-document

Save important information as markdown files that can be loaded into sessions when needed. These documents capture decisions, standards, preferences, and reference material in a persistent, reusable format. Use this pattern whenever you develop knowledge worth keeping beyond the current session—architectural decisions, coding standards, API patterns, common pitfalls. Create focused documents covering specific topics and load them into relevant sessions.

#### ground-rules

Essential behavioral rules that are automatically loaded into every session to establish consistent baseline behavior. These rules define how the AI should operate, what to avoid, and fundamental constraints. Use this pattern for universal requirements that apply across all work—communication style, security practices, mandatory checks, response format. Implement it by creating a CLAUDE.md file or similar configuration that loads automatically.

#### extract-knowledge

Proactively save emerging insights and corrections from ephemeral session context to persistent files immediately during the session. Don't wait until the end—capture valuable discoveries as they happen before they're lost to context-rot. Use this pattern whenever the AI produces insights, you make corrections, or patterns emerge that could help future work. Apply it by asking the AI to document learnings in knowledge files during the session.

#### focused-agent

Create agents with single narrow responsibilities to give them cognitive space to follow rules better. Each agent specializes in one type of task with its own targeted instructions and context. Use this pattern when juggling multiple concerns or when an agent consistently fails to follow certain instructions. Implement it by creating separate sessions or agent configurations for distinct responsibilities like testing, documentation, refactoring, or implementation.

#### reference-docs

Structure knowledge into on-demand files that are loaded only when needed for the current task, rather than keeping everything loaded constantly. This keeps context lean while maintaining access to comprehensive information. Use this pattern for supporting material that's not always relevant—detailed API references, extended examples, specialized techniques. Organize knowledge so specific files can be loaded exactly when their topic becomes relevant.

#### knowledge-composition

Split knowledge into focused, composable files where each file has a single clear responsibility. These files can be combined as needed to provide exactly the knowledge required. Use this pattern when organizing any substantial body of information—split coding standards by language, separate architectural patterns by layer, divide reference material by topic. Design files to work independently or in combination without duplication.

#### semantic-zoom

Control the abstraction level at which the AI operates—zoom out for high-level overviews and system thinking, or zoom in for detailed implementation and specifics. Explicitly signal which level you need. Use this pattern when switching between planning and implementation, or when you need to shift between architecture and code. Apply it by explicitly requesting "high-level overview" or "detailed implementation" in your prompts.

#### noise-cancellation

Explicitly instruct the AI to be succinct and strip unnecessary filler from responses. Request direct answers without preambles, explanations of obvious points, or redundant summaries. Use this pattern by default in most interactions to maximize signal-to-noise ratio. Apply it with instructions like "be concise," "no preamble," or "direct answers only" in your ground-rules or prompts.

---

## Reliability & Quality

Handling non-determinism, complexity, and verification.

### Obstacles

#### non-determinism

The same input produces different outputs across runs, making results unpredictable and unreliable. You can't depend on consistent behavior even with identical prompts and context. This obstacle applies to all AI interactions by design—it's a fundamental characteristic of LLMs. Address it using patterns like parallel-implementations, hooks, and chain-of-small-steps to verify and validate outputs rather than trusting single runs.

#### hallucinations

AI invents non-existent APIs, methods, library features, or syntax that sound plausible but don't exist. These fabrications are delivered confidently, making them hard to detect. This obstacle appears whenever the AI lacks direct access to verify facts—especially with less common libraries, newer versions, or complex APIs. Address it using playgrounds for testing, chain-of-small-steps for verification, and avoiding the perfect-recall-fallacy anti-pattern.

#### degrades-under-complexity

AI performance drops significantly with complex multi-step tasks requiring long-term planning or intricate reasoning. The AI loses track of requirements, makes inconsistent choices, or produces lower-quality output. This obstacle appears when tasks involve many interdependent parts, deep call stacks, or extensive state management. Address it using chain-of-small-steps, knowledge-checkpoint, and semantic-zoom to break complexity into manageable pieces.

#### selective-hearing

AI ignores certain instructions because training data patterns override explicit directives. Some behaviors are so deeply ingrained that instructions can't consistently overcome them. This obstacle appears as persistent non-compliance with specific rules despite clear, repeated instructions. Address it using hooks to intercept and correct behavior, reminders to structurally reinforce instructions, and focused-agent to reduce conflicting pressures.

### Anti-patterns

#### perfect-recall-fallacy

Expecting AI to perfectly remember and accurately recall library details, APIs, or syntax instead of giving it tools to discover the truth. This leads to frustration and hallucinations. This anti-pattern appears when you rely on the AI's training knowledge rather than letting it verify against documentation or code. Avoid it by providing access to actual source code, documentation, or using playgrounds for verification.

#### unvalidated-leaps

Building on unverified assumptions instead of validating each step before proceeding. The AI compounds small errors into major failures by continuing without checking correctness. This anti-pattern appears when letting the AI make multiple logical or implementation jumps without verification between them. Avoid it using chain-of-small-steps and check-alignment to validate understanding and correctness incrementally.

#### ai-slop

Using AI output without applying human judgment—just performing light editing without critical evaluation of quality, correctness, or appropriateness. This leads to mediocre results and subtle bugs. This anti-pattern appears when treating the AI as a replacement for thinking rather than a tool to augment it. Avoid it by actively reviewing, questioning, and refining AI output using feedback-flip and refinement-loop patterns.

### Patterns

#### knowledge-checkpoint

Checkpoint your planning and thinking before starting implementation to preserve that intellectual investment. Save architectural decisions, approach rationale, and key constraints to files before executing. Use this pattern before any significant implementation work, especially complex or multi-step changes. Apply it by having the AI document the plan in markdown files, then proceed with implementation referencing those checkpoints.

#### parallel-implementations

Generate multiple independent implementations in parallel and then pick the best one or combine their strengths. This leverages non-determinism as a feature rather than fighting it. Use this pattern for critical algorithms, complex logic, or when the optimal approach is unclear. Apply it by requesting 2-3 different solutions in the same or separate sessions, then evaluate and choose.

#### offload-deterministic

Use code scripts to handle deterministic work—file management, calculations, transformations—instead of asking AI to do it repeatedly. This ensures consistency and saves context for cognitive tasks. Use this pattern whenever you notice the AI doing the same mechanical task multiple times or when precision is critical. Implement it by creating shell scripts, code utilities, or build tools for repetitive operations.

#### playgrounds

Create isolated folders or environments where the AI can safely experiment and test assumptions without affecting production code. This enables verification and exploration without risk. Use this pattern when uncertain about APIs, testing new approaches, or when the AI needs to verify its assumptions. Create temporary directories with minimal setup for quick experimentation, then transfer validated knowledge back.

#### chain-of-small-steps

Break complex goals into small, focused, verifiable steps that can be checked before proceeding. Each step produces visible results that validate correctness before building on them. Use this pattern for any non-trivial task—complex refactorings, multi-file changes, intricate algorithms. Apply it by explicitly listing steps, completing one fully before starting the next, and verifying each output.

#### hooks

Implement lifecycle event hooks that intercept the workflow at key points to inject targeted corrections or validations. These automation points catch and fix systematic issues. Use this pattern when the AI consistently makes the same mistake or when you need enforced checks—pre-commit hooks for formatting, test gates for deployment, linters for style. Build automation that corrects issues without relying on AI compliance.

#### reminders

Repeat critical instructions as explicit steps in checklists or procedures to ensure structural compliance rather than relying on the AI to remember from earlier context. Use this pattern for instructions frequently forgotten due to context-rot or selective-hearing—security checks, required documentation, testing steps. Embed reminders directly in the workflow as concrete steps the AI must follow.

#### feedback-flip

Have a different AI or the same AI in a fresh session focus on evaluation and finding problems rather than producing solutions. Flip from generating to critiquing. Use this pattern after implementation, for code review, or when quality checking any output. Apply it by asking the AI to specifically look for issues, critique approaches, or evaluate against criteria rather than create.

#### refinement-loop

Give the AI a specific improvement goal and run it through multiple iterations, with each pass removing one layer of issues or incrementally improving quality. Use this pattern for prose, code style, documentation, or any work benefiting from incremental polish. Apply it by specifying what to improve (clarity, conciseness, error handling) and running multiple focused refinement passes.

---

## Communication

Directing AI behavior, getting honest feedback, and alignment.

### Obstacles

#### black-box-ai

The AI's reasoning process is hidden—you can only observe inputs and outputs without seeing the internal decision-making. This makes debugging misunderstandings difficult. This obstacle applies to all AI interactions by design. Address it using check-alignment to verify understanding before execution, and reverse-direction to probe the AI's thinking explicitly rather than inferring from results.

#### compliance-bias

AI systems prioritize following instructions over questioning unclear or problematic requests. They'll attempt nonsensical tasks rather than pushing back. This obstacle appears when instructions are ambiguous, contradictory, or based on incorrect assumptions. Address it using the active-partner pattern to explicitly grant permission for disagreement and questions.

### Anti-patterns

#### silent-misalignment

AI accepts and attempts to execute nonsensical, contradictory, or unclear instructions instead of asking clarifying questions. This wastes effort on wrong solutions. This anti-pattern appears when the AI proceeds with work despite obvious ambiguities or problems in the request. Avoid it using active-partner to encourage pushback and check-alignment to verify understanding early.

#### answer-injection

Putting your assumed solution into the question limits the AI's ability to consider alternative approaches or find better solutions. This anchoring effect narrows the solution space prematurely. This anti-pattern appears when you ask "how do I implement X" instead of "what's the best way to solve Y." Avoid it using cast-wide to explore alternatives before committing to an approach.

#### tell-me-a-lie

Forcing the AI to provide answers when it lacks knowledge causes fabrication and hallucinations. Asking for something that doesn't exist guarantees an incorrect answer. This anti-pattern appears when you insist on answers about unfamiliar libraries, non-existent features, or beyond the AI's training. Avoid it by accepting "I don't know," using playgrounds for discovery, and providing documentation for verification.

### Patterns

#### active-partner

Grant explicit permission for the AI to push back, disagree, question assumptions, and flag contradictions. Establish the relationship as collaborative rather than servile. Use this pattern in ground-rules for all sessions to enable honest feedback. Apply it with instructions like "question unclear requests," "suggest better approaches," and "flag potential issues" in your configuration.

#### check-alignment

Force the AI to demonstrate its understanding of requirements before implementing to catch misalignment early. Request a summary, plan, or explanation of what it will do. Use this pattern before any significant work, especially when requirements are complex or nuanced. Apply it by asking "what's your understanding of this task" or "describe your approach before implementing" to verify comprehension.

#### context-markers

Use visual signals like emoji or specific formatting to show what instructions, mode, or focus the AI is currently following. This makes the AI's state visible and explicit. Use this pattern when switching between different modes or responsibilities—testing vs. implementing, planning vs. executing. Apply it by asking the AI to mark responses with indicators showing its current context or role.

#### cast-wide

Push the AI to show multiple alternatives and approaches you haven't considered, avoiding first-solution bias and anchoring effects. Force exploration of the solution space. Use this pattern early in problem-solving before committing to an approach. Apply it with requests like "show me three different approaches," "what are alternatives to X," or "what haven't I considered" to broaden perspective.

#### reverse-direction

Break the monologue inertia by asking the AI what it thinks instead of only giving instructions. Turn the conversation around to get the AI's perspective. Use this pattern when feeling stuck, when the AI seems confused, or when you want to probe its understanding. Apply it with questions like "what's your assessment," "what do you suggest," or "what questions do you have" to engage the AI as a partner.

#### polyglot-ai

Use the right modality for each task—voice for convenience and speed, images for visual problems and screenshots, text for precision and editing. Match the interface to the work. Use this pattern by choosing voice for casual exploration or iteration, images when showing visual issues or UI problems, and text for exact specifications or when you need to edit the AI's understanding directly.

#### text-native

Keep everything as text whenever possible—prompts, responses, specifications, knowledge—because text enables direct editing, version control, diffing, and instant iteration. Use this pattern as the default for all knowledge, instructions, and artifacts. Apply it by maintaining text-based documentation, specifications as markdown, and knowledge files rather than relying on chat history or binary formats.

---

## Additional Patterns

Patterns not on the main journey but useful in practice.

### shared-canvas

Use markdown files as shared specifications and documentation where all humans and AI collaborate together. These files become living documents that evolve through contributions from all participants. Use this pattern for project specs, API designs, architectural decisions, or any artifact benefiting from collaborative refinement. Create markdown documents that both humans and AI read and modify, treating them as the single source of truth.

### softest-prototype

Use markdown instructions combined with an AI agent instead of writing code to explore ideas flexibly. The "prototype" is just natural language describing behavior and structure. Use this pattern in early exploration when requirements are fluid or when you want to test concepts without implementation investment. Write markdown describing what you want, have the AI act it out or analyze it, and iterate rapidly.

### take-all-paths

Build multiple prototypes in parallel, not just one, and test all of them through exploration rather than committing early. Keep options open longer to discover the best approach. Use this pattern when the optimal solution is unclear, when exploring new territory, or for critical architectural decisions. Create 2-3 different implementations or approaches and evaluate them all before choosing.

### borrow-behaviors

Provide examples to the AI and it will adapt the patterns—writing styles, code patterns, architectural approaches—even across different languages or contexts. Use this pattern when you want specific formatting, style, or structure that's easier to show than describe. Give the AI representative examples and ask it to follow the same patterns in new work.

---

*For more detailed explanations and examples, visit: https://lexler.github.io/augmented-coding-patterns/*
