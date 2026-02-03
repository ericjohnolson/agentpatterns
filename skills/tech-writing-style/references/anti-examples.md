# Anti-Examples: What Not to Write

These patterns weaken technical writing. When you catch yourself using them, rewrite.

## Contents
- Service language
- Announcing structure
- Hedging without information
- Lists of considerations without positions
- Passive voice hiding agency
- Filler phrases
- Repetitive sentence structure
- Definition-first openings
- Manufactured enthusiasm

## Service Language

**Avoid:**
> I'd be happy to explain dependency injection.
> Let me walk you through the architecture.
> I can help you understand this better.

**Problem:** You're writing a blog post, not responding to a support ticket. Service language positions you as helper, not thinker. It's filler.

**Instead:** Just explain it. "Dependency injection solves a specific problem..." The essay form doesn't need permission to begin.

## Announcing Structure

**Avoid:**
> In this article, I will first explain X, then discuss Y, and finally conclude with Z.
> This post covers three main topics.
> Before we dive in, let me set the stage.

**Problem:** This is throat-clearing. It tells the reader about the essay instead of starting the essay. Let the structure reveal itself through the content.

**Instead:** Start with your hook. Let section headers do the navigation work if needed.

## Hedging Without Information

**Avoid:**
> This might be a good approach in some situations, depending on various factors.
> Results may vary based on your specific context and requirements.
> There are pros and cons to consider.

**Problem:** These hedges communicate nothing. They're defensive writing—technically not wrong, but useless.

**Instead:** Hedge specifically when you hedge:
> This works well for stateless services. For anything with session data, the tradeoffs shift—you'll need sticky sessions or externalized state.

## Lists of Considerations Without Positions

**Avoid:**
> When choosing a database, consider:
> - Performance requirements
> - Scalability needs
> - Team expertise
> - Cost constraints
> - Compliance requirements

**Problem:** This is a checklist, not thinking. Anyone could generate this list. What value are you adding?

**Instead:** Take a position:
> For most teams starting out, PostgreSQL is the right choice. It handles more scale than you'll need for years. By the time you outgrow it, you'll understand your access patterns well enough to choose something specialized.

## Passive Voice Hiding Agency

**Avoid:**
> The decision was made to deprecate the API.
> Mistakes were made during the migration.
> It was determined that the approach was flawed.

**Problem:** Who decided? Who made mistakes? Who determined? Passive voice often hides responsibility—and hides the interesting part of the story.

**Instead:** Name the actor:
> We decided to deprecate the API after three months of trying to maintain backward compatibility.
> I made a mistake during the migration—I assumed the data was already validated.

## Filler Phrases

**Delete these:**
- "Actually" (unless contrasting with expectation)
- "Basically" (just explain it)
- "In order to" (use "to")
- "It's worth noting that" (just note it)
- "As a matter of fact" (just state the fact)
- "At the end of the day" (cliché)
- "Needless to say" (then don't say it)

These phrases feel like you're writing. They don't add meaning.

## Repetitive Sentence Structure

**Avoid:**
> Microservices are independent. Microservices are scalable. Microservices are complex. Microservices require careful design.

**Problem:** Monotonous rhythm. Each sentence the same shape deadens the prose.

**Instead:** Vary structure:
> Microservices offer independence—each service can deploy and scale on its own schedule. That flexibility comes with cost. The complexity isn't in any single service; it's in the space between them.

## Definition-First Openings

**Avoid:**
> Technical debt is defined as the implied cost of future rework caused by choosing an easy solution now instead of a better approach that would take longer.

**Problem:** You're writing a dictionary entry. The reader came for your perspective, not Wikipedia.

**Instead:** Start with experience:
> Last month we paid down some technical debt: four days to change something that should have taken an hour. That's when I started thinking about how we got there.

Let the definition emerge from context, or skip it entirely if the reader likely already knows the term.

## Manufactured Enthusiasm

**Avoid:**
> I'm really excited to share this framework with you!
> This is such a fascinating topic!
> I absolutely love this approach!

**Problem:** Enthusiasm should come from the content, not from exclamation points. Manufactured enthusiasm reads as marketing.

**Instead:** Let genuine interest show through the depth of your engagement with the topic. If you find something interesting, explain why—don't just assert excitement.
