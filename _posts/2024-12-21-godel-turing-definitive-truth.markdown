---
layout: post
title: " Why We Might Never Discover a Definitive Truth"
date: 2024-12-21 10:00:00 +0000
categories: 
---

# Why We Might Never Discover a Definitive Truth

I’ve always been curious about the world—the desire to figure out how it all works, to connect the dots, and to know the “why” behind it all. Could there be a single explanation for everything? Since my childhood, I always heard that we are looking to connect various theories, to find a single theory that explains everything. Yet, the deeper I look into this, the more I realize it might be something we can never truly reach.

Most likely there is something I understand, but both Gödel’s and Alan Turing’s halting problem lead in this direction. Their discoveries suggest there will always be limits to what we can know or prove, no matter how clever we get. Here’s what I’ve learned and why it leads me to believe the search for truth is endless—but also endlessly worthwhile.

---

## Gödel’s Incompleteness – Why Logic Has Gaps

Gödel’s work fascinates me because it starts with a simple idea: **rules and systems can’t explain everything.** Imagine a mathematical system that’s powerful enough to describe arithmetic (like the math we use to add, subtract, or multiply). Gödel proved that in such a system, some truths exist that cannot be proved using the system’s own rules.

He even built a statement to demonstrate this—essentially saying, “This statement cannot be proved.” If the system could prove it, it would create a contradiction. If it couldn’t prove it, then the statement was true but unprovable. Either way, the system had limits.

But it doesn’t stop there. Gödel also showed that no system can prove its own consistency—it always needs a bigger framework to confirm it’s reliable. And that bigger system always has its own limits. So, every time we think we’ve wrapped everything up neatly, new questions appear.

---

## Turing’s Halting Problem

Gödel wasn’t the only one to show that limits are built into the systems we create. Alan Turing, one of the pioneers of computer science, tackled a related problem: **can we build an algorithm that decides if any program will eventually stop running or keep going forever?**

The answer was **no**. Turing came up with a clever proof. In short, he imagined a program that would take any algorithm and decide whether it halts or runs forever. Then he designed a program that would do the opposite of what this “universal decider” predicted.

More Precise

Turing assumed there was a program, call it `H`, that could determine for any program `P` and input `I` whether `P` halts on `I` or runs forever. He then constructed a new program, call it `D`, which uses `H` as a subroutine but always does the opposite of what `H` predicts when `D` is run on its own code.

1. **D** takes an input `x`.
2. **D** asks **H** whether **D** halts on `x`.
3. If **H** says “yes, D halts on x,” then **D** enters an infinite loop.
4. If **H** says “no, D does not halt on x,” then **D** halts immediately.

Now consider what happens if we feed **D** its own code as input:

- If **H** claims “D halts on D,” then by step (3), **D** loops forever—contradicting **H**’s claim.  
- If **H** claims “D does not halt on D,” then by step (4), **D** halts—again contradicting **H**.

In either case, **H** is wrong. This contradiction shows that no universal decider **H** can exist.

The contradiction proved that no universal algorithm can solve this problem for all cases. It’s like a special case of Gödel’s findings. Turing’s Halting Problem shows that **there are questions no system can answer**.

---

## Physics and the “Theory of Everything”

This brings us to physics, where the dream of a “theory of everything” has captured imaginations for decades. Physicists have tried to unite the rules of quantum mechanics and general relativity. The two theories work brilliantly in their own areas but **clash** when we try to combine them.

The leading candidates, like string theory or loop quantum gravity, offer promising ideas, but none have solved every problem or passed every test. And if a theory of everything relies on mathematics, Gödel’s limits may come into play. Even the most elegant theory might leave some truths about the universe **unprovable**.

There’s also a practical problem: to test these theories, we’d need **experiments at energy levels or scales** far beyond what we can currently achieve.

---

## Why Keep Searching?

If we can never find a final truth, why bother? It’s because every step forward matters. Each new theory doesn’t just solve old problems—it opens a new door, leading to at least one new question.

- Newton’s laws weren’t the final word, but they let us build bridges and send rockets to space.  
- Quantum mechanics isn’t complete, but it gave us semiconductors and lasers.

Even when our theories fall short of perfection, they give us tools that transform the world.

Here’s the part I find beautiful: the fact that we’ll **never** find the final truth **doesn’t** make the search meaningless. It makes it **endless**—and that’s a good thing. If there were nothing left to discover, where would curiosity go?

Gödel, Turing, and the struggles of physics remind us that every answer leads to more questions. We’ll never explain everything, but we’ll keep learning.

But perhaps this analogy does not fit perfectly, as physicists define **models** of reality, not fundamental truths. Thus, even if a model is partial or approximate, it may be the best one available. Physics has historically often succeeded by discovering uniting principles (e.g., Newton’s laws combined celestial and terrestrial mechanics, Maxwell’s equations united electricity and magnetism).


