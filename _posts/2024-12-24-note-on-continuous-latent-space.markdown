---
layout: post
title: "Note on paper Training Large Language Models to Reason in a Continuous Latent Space"
date: 2024-12-24 19:00:00 +0000
categories: ai machine-learning cot
---

**Paper Title:** Training Large Language Models to Reason in a Continuous Latent Space

**Link:** [https://arxiv.org/abs/2412.06769](https://arxiv.org/abs/2412.06769)

## 1) Motivation and Main Idea

Large Language Models (LLMs) usually do what’s called “chain-of-thought” (CoT) reasoning, where they generate the intermediate reasoning process as plain text tokens. For instance, if the model is solving a math problem, it might literally spell out: “Let’s compute the difference, then multiply, ...” and so on, before giving the final answer. While CoT is powerful, the authors points that forcing the model to express its entire thought process in text might be suboptimal because:

Not all tokens carry equal “reasoning load.” Some tokens are just for grammatical or stylistic fluency. Others are crucial for the next logical step. But in typical CoT, the model is forced to spend roughly the same computing budget on every word token.

Language may limit reasoning flexibility. In human cognition, areas involved in pure logical or mathematical reasoning often show less overlap with the language centers of the brain. Language is generally optimized for communication rather than thinking. So, maybe we can give a model the freedom to “think silently” in some continuous (non-linguistic) space, only converting the final conclusion back into text once it’s done thinking.

## 2) Key Innovation: Coconut (Chain of Continuous Thought)
Coconut is a new paradigm that lets an LLM do part (or all) of its thinking in a continuous hidden state space, rather than in discrete text tokens. Concretely:

The LLM can switch between “language mode” (standard text token generation) and “latent mode” (reasoning in continuous hidden states).
In latent mode, the output hidden state of the last token is fed back into the model directly as an input embedding for the next step—no tokenization or text decoding in between.
Ultimately, the LLM can return to language mode to produce an answer or an explanation.
This approach effectively “detaches” large portions of the reasoning from the requirement to be spelled out in text. The hope is that the model can reason more flexibly and efficiently, focusing computation where it’s needed.

## 3) Coconut in Practice
### 3.1) Model Basics

A standard language model, like `GPT-2`, has:

- Token embeddings: Turn discrete tokens (words or subwords) into vectors.
- Transformer layers: Process sequences of embeddings.
- Final projection (language model head): Predicts the next token distribution from the last hidden state.

### 3.2) Switching Between Language and Latent Modes

Language mode: normal text generation—model outputs next token.
Latent mode: each “token” step is actually the model’s last hidden state. There are no discrete text tokens here.

**They introduce two special tokens:**
- `<bot>` (“begin of thought”): signals when latent mode starts.
- `<eot>` (“end of thought”): signals the end of latent mode, returning to normal text generation.

When `<bot> `is encountered, the model no longer maps hidden states to text tokens. Instead, it takes the hidden state from the previous step (which is a continuous vector) and feeds it as the next “input embedding.” This can last for as many steps as we want, until <eot> is encountered.

### 3.3) Training Procedure: Multi-stage Curriculum

Training Coconut from scratch can be tricky because there’s no direct supervision telling the model exactly how to use those continuous states to reason. The authors adopt a multi-stage curriculum (inspired by “iCoT,” another method for compressing chain-of-thought):

- Stage 0: Train the model as a normal language model on data where each question has a “chain of thought” plus the final answer.
Stage k: Gradually replace some (or all) of the language-based reasoning tokens with “continuous thoughts.” That means, for the k-th stage, the first k steps of the chain-of-thought are converted from language tokens into the new “latent steps.”
By the final stage, all or most of the language reasoning tokens are replaced by continuous thoughts. Crucially, the authors mask the loss on these latent steps (so there’s no direct “label” for what the continuous state should be), but the model is still trained to produce the final textual answer correctly—so it has to learn to do some hidden reasoning to get the right answer.

## 4) Experimental Results
They test Coconut on three main reasoning benchmarks:

- GSM8k (Math Word Problems)
- ProntoQA (Logical Reasoning)
- ProsQA (A newly proposed more complex logical QA dataset using randomly generated DAGs).

### 4.1) Accuracy and Token Efficiency
They compare Coconut with baselines:

- No-CoT: Train the model to directly produce the final answer (no chain-of-thought).
- CoT: Use the full chain-of-thought in language.
- iCoT: Another method that gradually internalizes CoT, but eventually produces no tokens for the reasoning steps (only final answer).
- Pause Token: Insert a special <pause> token in place of hidden states (similar to hidden steps, but still a discrete token).

**Findings:**

- Coconut outperforms “no-CoT” consistently, showing that some form of step-by-step structure (even if hidden) helps.
- Coconut matches or exceeds CoT on tasks that require heavy logical planning and backtracking (e.g., the more complex logical tasks).
- On GSM8k (math reasoning), Coconut yields a substantial accuracy boost over no-CoT. Although it may not beat a carefully tuned CoT on every single dataset, it demonstrates that “latent chaining” provides a form of depth—similar in spirit to how language-based CoT recycles model outputs as new inputs.
- Coconut can generate fewer textual tokens than CoT at inference time (because part or all of the “reasoning” is never spelled out in text).

### 4.2) Emergent “Breadth-First Search” (BFS) Pattern

An interesting observation is that when the model reasons in continuous space, it seems to encode multiple possible next steps simultaneously in its hidden states—rather than committing to a single next-step token. This is reminiscent of a BFS or partial expansion of a search tree:

- The model’s first latent state can represent multiple plausible options.
- In subsequent latent steps, the model narrows down these options.
- When forced (by the experimenter) to switch back to text mode at certain points, the model reveals that it’s maintained a distribution over multiple next-step choices. This effectively allows backtracking or multi-branch exploration internally, which is much harder with purely autoregressive language CoT.

## 5) Analysis: Why Latent Space Reasoning Helps
Hard Steps vs. Easy Steps: In typical CoT, every single token requires the same forward pass of the Transformer, even if it’s just a filler word (“therefore,” “next,” etc.). Latent mode can skip text generation and focus more on the steps that matter.

Parallel vs. Deterministic Search: In normal text generation, if you produce a next token, you’re locked into that path. But in continuous space, the model can keep track of multiple alternatives—and only “collapse” to a final textual path at the end.

Better Planning: For tasks with lots of branching (like a big logical proof or complex graph), it’s advantageous to not commit early. Instead, you wait until you’re more certain of the correct path, which the continuous representation can do implicitly.

## 6) Limitations and Future Directions
Training Complexity: The multi-stage procedure can be slower, as they need multiple forward passes to insert each continuous thought, especially at higher numbers of latent steps.

Need for Curriculum: Simply turning on continuous reasoning from the start doesn’t work well; the model needs guidance from partial chain-of-thought examples. This raises questions about how to do it at massive scale, or how to do it if you have no chain-of-thought supervision at all.

Interpretability: Continuous thoughts are less interpretable than text. Although we can “decode” them back to words for debugging, that’s not guaranteed to reflect exactly what the hidden state is “thinking.”

Combining with Other Techniques: The authors mention that in principle you can combine Coconut with other approaches (like “iCoT,” advanced tree-search methods, or “Self-Consistency”). They also propose exploring a “hybrid” approach (some steps in continuous space, others in text).

## 7) Importance
**Novelty:** This work is one of the first to propose letting an LLM do large portions of its reasoning directly in hidden states. While people have considered “pause tokens” or “latent space interventions,” Coconut’s direct feed of the last hidden state as the next input embedding is a more radical approach to bridging the output-to-input loop at the vector level.

**Implications for Efficient Reasoning:** Potentially less text is produced during inference, which can be faster or more efficient in real deployment.

**Implications for Model Capabilities:** The BFS-like search emerges implicitly, which is pretty cool—it suggests LLMs can spontaneously explore multiple lines of reasoning at once, given the right architecture and training scheme.

**Future Directions:** Possibly (a) pretraining with continuous thoughts so that from day 1, the model learns how to do partial hidden steps, or (b) mixing continuous + textual reasoning in a single approach. Also, investigating whether latent reasoning can push LLMs to solve even harder reasoning tasks remains an open question.

Traditional large language models express their full reasoning out loud in text tokens. This new approach, “Coconut,” lets the model do “silent thinking” in continuous hidden states, then only speak up in language when it needs to. This can help the model handle more complex reasoning without getting bogged down by generating each intermediate token in text—and can allow exploring multiple reasoning paths in parallel before deciding which is correct. 
