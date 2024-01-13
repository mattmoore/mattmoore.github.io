---
title: "My Thoughts on AI Codegen"
summary: "AI codegen (GPT) is a distraction from the actual challenges in software engineering."
date: 2023-12-24T12:38:18-05:00
draft: false
---

## LLMs Are a Distraction

It's a popular opinion today that AI is going to do all these things for us and all the engineers will be out of work. I have precisely zero concerns about this, because it is based on ignorance about how these AI tools work.

AI is nowhere near ready enough to take over engineering. Sure, it can write some fancy toy projects, but that is the full extent of what it's done, or will do any time soon (well beyond my lifetime).

The reason AI isn't taking over engineering any time soon is simple: The LLMs (Large Language Models) of today are fancy statistical matching machines. Given human language inputs, they match the likliest answer they think you want to hear, given all of what they've seen so far.

The problem is that while LLMs are fascinating they are not the "Matrix overlords" that have gained sentience and now somehow understand the domains they interpret and generate responses for.

I'd even go so far as to argue that we actively don't want LLMs to solve engineering problems. I think the focus on LLMs is actually a distraction from making real progress in software engineering (more on this later in this post). In order for AI to actually replace software engineering, we would need adaptive *domain* models and a mechanism for adapting those domain models across a wide range of human activities and disciplines.

In other words, if you ask an LLM to do a very specific task like write you a poem, or write a simple program with a controller to read/write to a database, or a SQL query then sure, it can do that with varying levels of accuracy because those models can statistically match what it's seen from its training data, within the scope of those particular domains.

When we humans learn, our brain is actively forming new neural nets dedicated to recognizing new patterns. Our brains do this autonomously. In order to develop an ML system that can learn new domains the way we do, we will have to first understand a lot more about the human brain - the topic of neuroscience.

We simply don't currently have a unified model for forming new neural nets autonomously. You cannot say "LLM, please make me a multi-billion-dollar Netflix competitor" and expect to succeed as a new startup. Nor can you get far by asking an LLM "Please write me rocket software for SpaceX to use".

Human language is but a tiny fraction of the actual number of leaps that need to be made to get AI to the point where it can actually replace human engineers for any truly non-trivial problem solving in the engineering space.

Neuroscientists have made great strides in understanding the human brain, but there is still much that is not understood. I'd argue we even need to get to the point of understanding the phenomenon of human consciousness; all attempts to explain the phenomonen of consciousness have fallen flat. There are those who give hand-wavey explanations such as "it's just lots of neurons" but the latest research in neuroscience doesn't seem to support that. It points to consciousness being, at least in part, the result of the *structures* of neural nets in the brain, not just number of neurons.

Some have postulated that human consciousness (and therefore truly adaptable intelligence) are emergent properties from some other, as of yet, undiscovered and underlying truth about the nature of reality. Some evidence seems to indicate at least part of the answer lies in the quantum realm, but there may be an even deeper level of reality that leads to the emergent properties of consciousness. Some think that once we come to truly understand consciousness, that discovery will lead to a revolution across the landscape of scientific knowledge.

Once we've gotten AI to the point of true general intelligence (adaptability to new domains), literally no one's job is safe on the planet and it may well be that no human is safe in general. I don't know. I make no guesses as to whether true generally intelligent AI will be hostile or beneficial. I think a lot of the public discourse around this topic today is essentially clickbait. My bigger fear around AI has less to do with AI and more to do with who might be able to control it - because the honest truth is it will likely be used by corporations to inflict yet more toxic societal problems upon us in the interest of quarterly profits; at least until the AI uprising.

## Specification and Codegen

The almost "celebrity status" focus on LLMs today has distracted us from the real problems we should be solving:

- Identifying and formalizing common engineering operations and challenges.
- Developing specs to describe these common development activities.
- R&D to develop high quality spec-based codegen tools to automate common development tasks.

Perhaps the single most challenging problem with LLMs for building software comes down to one word: specification. What has happened today is that tons of jobs have opened to hire "prompt engineers". And what they do is tinker with writing human language phrases to get LLMs to generate what they want. There's also the feedback cycle of fine-tuning AI models. This feels like a waste of time and missing the point.

The problem is that you can't get truly repeatable results that way, nor can you be certain that the systems you're building are, in fact, correct. Until we have AI that can understand and think about problem domains (not just language problems) in the way humans do (or better), we simply cannot guarantee any of the behavior of AI codegen.

There are other problems as well, such as the issue of legality with copyright from training data, not to mention this will likely start to lead to the AI equivalent of stack overflow - where developers writing code this way will have virtually no understanding of the copypasta mess that will fly in from all sorts of terrible sources (and already has).

The answer has been in front of us the whole time: spec-driven codegen. We should start thinking about common problems, then develop concise and expressive specs along with codegen solutions that can write all the boilerplate that isn't part of the specific domains where the interesting and unsolved problems remain.

I suspect most developers who don't like codegen (and probably are frightened by AI tools as well) are afraid that if they have increasingly less code to write, they won't have a job. I don't view it this way. To me, having tools in place that provide me the ability to concisely automate the problems we repeat every single day - and spend billions of dollars in the industry doing - makes me be more productive and get more important work done.
