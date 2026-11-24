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

## 3. Present one fragment, explained in place

Each turn presents exactly one fragment and ends with a short prompt.

Put the explanation inside the content, not in a block after it. Cut the fragment into small pieces at natural seams: a statement, a condition, a call, or a sentence or clause of prose. Show each piece verbatim, with a short note directly below it. Each note then sits next to what it describes, so the reader never has to look back up to work out what a paragraph refers to. Read top to bottom, the pieces make up the whole fragment, in order. This also reads better: the content and the commentary take turns, rather than a wall of code followed by a wall of text.

Use this shape:

````markdown
### Fragment 3 of 9 · Backoff calculation
`src/net/client.ts:42-47`

```ts
const exp = base * 2 ** attempt;
```
Exponential backoff: each retry waits twice as long, so a struggling server gets room to recover quickly.

```ts
const delay = Math.min(exp, cap);
```
The cap keeps waits bounded. Without it, ten failures would already mean a wait of minutes.

```ts
return delay / 2 + Math.random() * (delay / 2);
```
This is "equal jitter", a name AWS popularised. Randomising half the wait stops many clients from retrying in lockstep.

**Overall:** a textbook retry policy. One thing to question: `attempt` is never reset after a success.

---
**Next?** next · deeper · ask a question · jump to … · stop
````

**The pieces**

- Quote every piece exactly as it currently is. Re-read the file before each fragment, because the user may be editing it while you walk. Never paraphrase, trim, reformat, or add comments to the content.
- Code goes in fenced blocks with the language tag. Put the location (`path:start-end`) once, in the header, so the user can click or jump to it.
- Prose pieces go in blockquotes. To draw the eye to the phrase that matters, **bold it inside the quote**, and say once that the emphasis is yours.
- Not every line needs a note. If a piece has nothing worth saying, merge it with its neighbour. A fragment of one to three lines can stay as a single piece.

**The notes**

- Keep each note simple and short, one to three sentences. Write plainly and warmly, as if you're sitting beside the user. If a term needs defining, define it in a few words.
- Most notes say one thing. Pick whichever matters most for that piece:
  - **Why it is this way**: the reason, the constraint, the trade-off, or the alternative that wasn't chosen.
  - **Roots**: the pattern, algorithm, standard, paper, or convention it comes from.
  - **Idiom**: whether this is the normal way to do it in this language or field, or a departure from it.
  - **Connections**: how it ties to an earlier fragment ("the counter from fragment 1").
  - **What to notice**: a subtlety, a likely bug, or an assumption worth questioning. Be candid about these.
- Don't narrate what the content plainly says. The user can read it. Explain what they can't see.
- Aim every note at the user's question from step 1.
- Separate evidence from inference. If a commit message, comment, issue, or citation says why something was done, cite it briefly (`git log -L` and `git blame` are often worth running for code). If you're reasoning from the code or text itself, say so ("most likely because…").
- Close with an **Overall** line only when something spans the whole fragment, such as a thread through it or a concern about it. Otherwise leave it out.

## 4. Wait, then respond

End the turn after the prompt. Don't continue to the next fragment in the same message. The pause is the point of the skill.

Use a plain-text prompt here, not a structured question dialog. A dialog can cover the explanation the user is trying to read.

Take the user's reply in whatever words they use:

- **next / yes / go on / an empty-ish reply**: present the next fragment.
- **deeper**: stay on this fragment and go one level down. That might be the history, a worked example, the alternative implementation, or tracing a call into another file. Use the same in-place style, then prompt again.
- **a question**: answer it in the context of the current fragment, then prompt again. Don't move on until they ask to.
- **back / jump to X / skip**: go where they ask. Update the fragment count if the route changes.
- **change of focus**: if the question or scope shifts mid-walk, re-plan the remaining route and show the new route in one or two lines.
- **stop**: end the walk with the closing below, cut short.

## 5. Close the walk

After the last fragment, or when the user stops, give a short closing:

- The thread that runs through the fragments, as two to five sentences, tied back to the user's original question.
- Anything you flagged as questionable along the way, gathered in one place.
- One or two natural next walks, such as "the caller in `server.ts`" or "§4, which this section sets up". Offer them; don't start one.
