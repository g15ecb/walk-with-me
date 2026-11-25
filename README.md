# walk-with-me

An agent skill for studying code, documents, specs, or any other material **one fragment at a time**.

Say *"walk with me"*. The agent looks at what you're working on and asks what to focus on. It then shows one fragment at a time, annotated in place, and explains why that fragment is the way it is: the logic, its roots in the wider field, and the idioms it follows. In code, the notes are comments in a syntax-highlighted block. In documents, they are highlighted phrases with sticky notes pinned beneath them. After each fragment it waits for you to say *next*, *deeper*, ask a question, jump elsewhere, or stop.

## Install

With the [skills](https://skills.sh) CLI:

```sh
npx skills add https://github.com/g15ecb/walk-with-me
```

To install from a local checkout:

```sh
npx skills add ./path/to/walk-with-me
```

## Use

```
walk with me
walk me through src/scheduler/
step through section 3 of docs/design.md with me, I want to see whether the argument holds
```

After each fragment, you choose what to do next from a set of options: next, go deeper, back, or stop. You can also type a question. If an agent has no options picker, reply in text instead (`next`, `deeper`, a question, `back`, `jump to …`, or `stop`).

## Layout

```
skills/
  walk-with-me/
    SKILL.md
```
