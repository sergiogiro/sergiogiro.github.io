--- 
layout: page
title: Do computers need to learn about code?
excerpt: It sounds paradoxical that machine learning for code doesn't take
         into account the logical/arithmetic semantics of code (yet)
---

Over the last years the advances in the area of "Machine Learning for code"
have been amazing. Researchers in this area feed algorithms with a ton of code
so that "machines" learn models that can be used to detect bugs, autocomplete
expressions, generate documentation, search for code beyond text matching, and
much more.

There's, however, something around this research that looks paradoxical to me.
I'll start with some context.

Most techniques of machine learning for code rely on models that have been
developed for unstructured domains, or domains lacking the formal semantics of
code. In fact, many models for bug detection would be able to catch a bug like
the one in this fragment:

    age = user.getAge();
    name = user.getName();
    surname =  user.getName();

There's a trivial pattern of words appearing both on the left and the
right-hand side of the assignment, and this pattern fails to hold in the third
line. Even someone who doesn't know much about programming is able to tell the
problem if they are told that there's a problem in these lines. Current
techniques are able to spot the problem without the need to specify the pattern
explicitly: based on the concept of naturalness you can train a model with a
lot of code (that is assumed to be "mostly correct") and, if you do the right
magic tricks [link xkcd], the model learns how is it that "natural" code looks
like, and you can flag parts of code that the model considers unnatural. (And
that's incredibly cool, and it would have been impossible without the advances
in (graphics) processor speed, and all the open source code on which these
algorithm are commonly trained, and all the research into machine learning
models, but...)

In contrast, I don’t know of any technique that would be able to spot a
problem in the following code fragment:

    i = firstElemIndex;
    while(i < elems.length) {
        skippedElems = elems[i].doSomeProcessing();
        i = i + skippedElems;
    }
    print(“Last element is: “ + elems[i].str()) 

In this hypothetical code fragment, we traverse an array as to process its
elements, skipping some of the elements depending on the result of the process.
Looking at the program attentively, we can see that in the `print` line we'll
be referring to an element beyond the length of the array elems, i.e. getting an
out-of-bounds error. In order to spot this error the model needs to know that:

- After a while loop, the negation of the guard holds
- The negation of the guard `i < elems.length` is `i >= elems.length`
- If you access an index `i >= elems.length`, that’s an out-of-bounds error

The deduction we need as to establish that this code is wrong is not involved
at all: (human) deductions of behaviours of programs can take a thick portion
of a book. So it’s quite surprising that we haven’t developed anything that
performs such a simple analysis (except for the good old formal methods
techniques, that haven't found much success beyond some sectors
like critical systems).

If we try to establish an analogy with human perception, it seems that we’ve
been developing more "primitive" tasks, like pattern recognition, and we’re
lacking more "advanced" tasks like deductive reasoning. If we go by the common
misconception [link] that the the left brain is analytical and the right is
intuitive, we could say that we've been looking more into the right side.
Trying to find a more realistic view of the brain, I was recently surprised to
read this theory about the prefrontal cortex, a brain area that is more
predominant in intelligent mammals: "cognitive control stems from the active
maintenance of patterns of activity in the prefrontal cortex that represents
goals and means to achieve them. They provide bias signals to other brain
structures whose net effect is to guide the flow of activity along neural
pathways that establish the proper mappings between inputs, internal states,
and outputs needed to perform a given task" (Miller EK, Cohen JD (2001). "An
integrative theory of prefrontal cortex function". Annu Rev Neurosci. 24:
167–202. I must admit I didn't go that deep in my search, I took the quote from
the Wikipedia page).

It's interesting how this function of the prefrontal cortex resembles a
computer: it has inputs, internal states, and outputs, and paths that starting
from the input traverse internal states as to obtain the output.

And so the paradox I wanted to expose is the following: don't computers know
about code already? Somehow, it sounds like the "analytic/logic" part
should be the simplest one to leverage (because it’s already there), yet it
seems to be the one we haven't explored yet.

There are some thoughts that I can sketch as to why this has been happening, yet
none of them seems to completely convince me:

- Computers understand code exactly as it is, but in order to spot bugs what
they need to know is what are the "glitches" of human perception that could 
ause problems. This is interesting as it somehow underlines the idea that a bug
is somehow a misunderstanding between a computer and a program. The human
writing the fragment with a loop and an out-of-bounds error understood that the
value after the loop exited was the last one to be processed.

- Computers know what to do with a piece of code only if they have the full
context. You can't execute "just the fragment with the loop and the print
statement" in isolation. (And if you create a full realistic context as to
execute it, well, you are just testing and not analysing anymore.)
Yet the fragment can still be (humanly) analysed in isolation since, no matter
what context it's in, it will give an error if the print statement is reached.
 
- It's just a people/communities problem. If so, we could be close to the end of
the paradox: I see more and more people who used to work in formal methods
moving towards statistical methods, and even problems like SAT that are "rigid"
in nature (after all, a formula is either satisfiable, or it is not) have
recently found benefits from approximations, with applications to programming.
If that's the case, and it's happening right now, we are no doubt living
interesting times.

I'd be happy to discuss any other answers or ideas you might have.
This is me on Twitter: [`_double_free`](http://twitter.com/_double_free).
