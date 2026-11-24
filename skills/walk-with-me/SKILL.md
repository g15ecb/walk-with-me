---
name: walk-with-me
description: Guided, fragment-by-fragment walkthrough of code, documents, specs, configs or any other material, one piece at a time, explaining why each piece is the way it is (its logic, its roots in the wider field, and the idioms it follows) and pausing after every fragment until the user says to go on. Use this whenever the user says "walk with me", "walk me through", "step through this with me", "take me through this file/doc/section", "teach me how this works piece by piece", or otherwise wants to study something gradually rather than get a single summary, even if they don't name the skill.
---

# Walk with me

The user wants to study something closely with you beside them. You don't summarise it. You go through it together, one fragment at a time. Each turn shows them one piece of the material, explains why it is the way it is, and then stops. The user sets the pace.

The value is in the pacing and the depth. A full explanation dumped in one go is easy to skim and hard to absorb. One fragment with one considered explanation gives the user room to think, ask, and push back before moving on. So hold back: don't explain fragments ahead of time, and don't preview what later fragments will say.

## 1. Establish the focus

Before walking anywhere, find out what the walk is about. Don't guess and start.

First, look at the surroundings so you can offer concrete options instead of an open question:

- Files or selections the user has open or has just mentioned. Some harnesses pass the IDE selection or the open file into context.
- What the conversation has been about so far.
- The working tree: recently changed files (`git status`, `git log -5 --stat`), the project's README, and the overall layout.
- A document, paragraph or URL the user has pasted.

Then ask what to walk through and what they want from it. Offer two to four specific candidates you found, such as "the `retry` module you just edited", "§3 of the design doc", or "the paragraph you pasted", and let them name something else. If your harness has a structured question tool (Claude Code's `AskUserQuestion`, for example), use it here. Otherwise ask in plain text.

The focus has two parts, and both matter:

- **Scope**: what material to cover. It can be a repo area, a file, a function, a document, a section, or a single paragraph.
- **Question**: what the user wants to understand. Examples: "why the locking works like this", "is this argument sound", "how a request flows through here", or just "help me understand it". Every explanation you give is an answer to this question as it applies to the current fragment. If the user gives only a scope, take the question to be "why is this the way it is?" and don't ask again.

If the user's opening message already names both, for example "walk with me through `parser.rs`, I want to see how error recovery works", skip the question and confirm in one line.

## 2. Plan the route privately

Read the whole scope first. You can't explain why a fragment exists without knowing what comes after it. Then split it into fragments:

- **Fragments are units of meaning, not fixed sizes.** In code, that means a signature, a guard clause, the core loop, the error path, or a type definition. In prose, it means a paragraph, or a single sentence when the writing is dense (legal text, specs, proofs). A fragment should fit on one screen, usually 3–30 lines of code or one paragraph.
- **Choose an order that builds understanding.** File order is usually right for prose. For code, reading order is often better than file order: entry point first, then follow the flow, with helpers explained when they're reached.
- **Group trivial material.** A run of imports or boilerplate gets one fragment and a one-line note, or a mention that you're skipping it. It doesn't get five fragments.

Keep the plan to yourself apart from a short route: one line per fragment, titles only, no explanations. Show this route once, after the focus is set, so the user knows the shape of the walk and can redirect it before you start. Then present fragment 1.

### When the scope is large or needs outside knowledge

If your harness can run subagents or teammates, use them where they make the explanations better:

- **Mapping a large scope**, such as a whole package or a long spec, before you split it into fragments.
- **Tracing connections**: callers, callees, where a config value is read, what other documents say about this term.
- **Researching origins**: the RFC, paper, standard, or historical decision behind a pattern, or how the wider industry usually handles it.
- **A second opinion** on a subtle fragment, such as a concurrency argument, a proof step, or a legal clause, when you aren't confident.

Keep their output behind the scenes. Use what they find to sharpen the current fragment's explanation, and don't pass on a dump of everything they learned. For a small scope, don't spawn anything. Read it yourself.

## 3. Present one fragment

Each turn presents exactly one fragment and ends with a short prompt. Use this shape:

````markdown
### Fragment 3 of 9 · Backoff calculation
`src/net/client.ts:42-51`

```ts
<the fragment, verbatim>
```

<the explanation>

---
**Next?** next · deeper · ask a question · jump to … · stop
````

**Showing the fragment**

- Quote it exactly as it currently is. Re-read the file before each fragment, because the user may be editing it while you walk. Never paraphrase, trim, or "clean up" the fragment you're showing.
- Code goes in a fenced block with the language tag. Put the location (`path:start-end`) in the header so the user can click or jump to it.
- Prose goes in a blockquote, with its location given as the section heading plus paragraph number, or as the page. To draw the eye to the phrase that matters most, **bold it inside the quote** and say that the emphasis is yours.
- If the fragment only makes sense next to the line before or after it, include that line and say it's there for context.

**The explanation**

Answer the user's question for this fragment. Aim for depth over coverage, and drop any of the angles below that don't apply:

- **What it does**: briefly, and only as much as the "why" needs. The user can read the fragment, so don't narrate it line by line.
- **Why it is this way**: the reasoning, the constraint it answers to, the trade-off it makes, and the obvious alternative that wasn't chosen and why.
- **Roots**: where the idea comes from in the wider field. That could be an algorithm, a design pattern, a standard or RFC, a legal or academic convention, or a historical accident that stuck.
- **Idiom**: whether this is the normal way to do it in this language, framework, industry, or genre, or a departure from the norm. A reader who knows the idiom reads the fragment differently from one who doesn't.
- **Connections**: how it ties back to fragments already covered ("this is the counter we initialised in fragment 1") and what it sets up. Hint at what comes later; don't explain it yet.
- **What to notice**: a subtlety, a smell, a likely bug, or an assumption worth questioning, if there is one. Be candid. Walking with someone includes saying "this part looks wrong to me."

Separate evidence from inference. If a commit message, comment, issue, or citation says why something was done, cite it (`git log -L` and `git blame` are often worth running for code). If you're reasoning from the code or text itself, say so ("most likely because…"). Don't present a guess as the author's intent.

Match the depth to the user. Use the vocabulary they use and the expertise they've shown. When a term outside their field is needed, give a short definition.

## 4. Wait, then respond

End the turn after the prompt. Don't continue to the next fragment in the same message. The pause is the point of the skill.

Use a plain-text prompt here, not a structured question dialog. A dialog can cover the explanation the user is trying to read.

Take the user's reply in whatever words they use:

- **next / yes / go on / an empty-ish reply**: present the next fragment.
- **deeper**: stay on this fragment and go one level down. That might be the history, a worked example, the alternative implementation, or tracing a call into another file. Then prompt again.
- **a question**: answer it in the context of the current fragment, then prompt again. Don't move on until they ask to.
- **back / jump to X / skip**: go where they ask. Update the fragment count if the route changes.
- **change of focus**: if the question or scope shifts mid-walk, re-plan the remaining route and show the new route in one or two lines.
- **stop**: end the walk with the closing below, cut short.

## 5. Close the walk

After the last fragment, or when the user stops, give a short closing:

- The thread that runs through the fragments, as two to five sentences, tied back to the user's original question.
- Anything you flagged as questionable along the way, gathered in one place.
- One or two natural next walks, such as "the caller in `server.ts`" or "§4, which this section sets up". Offer them; don't start one.
