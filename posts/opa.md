---
layout: post
title: Open Policy Agent (Highlighting the Policy Bit)
author: RandomByte
date: 07-11-2025
---

It is a bit crazy how long it can take me to siot down and write these blogs (it's been almost 4 years). I will spare you the promises of writing more often (though, truth to be told, I *do want* to start writing more regularly). With that out of the way, let us dig in! 

[Open Policy Agent (or OPA)](https://www.openpolicyagent.org/) is an open source policy engine designed to help with the management of __policies__ across your technology stack, in an attempt to improve security and auditability. 

At its core, the concept is deceptively simple: You have a binary file that receives a `JSON` file, and a `rego` policy (a small code snippet describing what rule to evaluate on that data). The policy engine (OPA binary), then gives you an answer: yes or no. In other words, it tells you whether that `JSON` object satisfies the requirements you have defined in Rego. This decision can be then fed to other services, systems, pipelines or processes that act accordingly. 

Borrowing the usual OPA architecture diagram (found in [here](https://cloudnativenow.com/features/introduction-to-open-policy-agent-opa/)):

![OPA Arch](/assets/images/opa_arch.svg)

We can see that OPA's architecture is simple, but that's precisely what makes it powerful. It is flexible enough to live anywhere in your stack: from CI/CD pipelines to Kubernetes clusters, and from REST APIs to your cloud infrastructure. 

In this blog I will try to dig into the concepts of **policy**, **policy engine**, the **Rego policy language**. We will look at practical examples for both technical readers (with code snippets) and organizational leaders interested in compliance and governance.

Because ultimately, tools like OPA do not just help us write safer code, they also can help organizations to codify their own internal requirements, ensuring trust.


- [What is a Policy, Really?](#what-is-a-policy-really)
- [Enter, OPA](#enter-opa)
- [From Logic to Policy: How OPA Evaluates Decisions](#from-logic-to-policy-how-opa-evaluates-decisions)

# <a name="policydef"></a>What is a Policy, Really?

Before going any further, I believe it’s important to clarify the terms we’ll be discussing. Part of this blog is dedicated to answering a simple but essential question: what is a policy?

[The Merriam-Webster dictionary](https://www.merriam-webster.com/dictionary/policy) offers several definitions, but the one I like the most is:

>"a definite course or method of action selected from among alternatives and in light of given conditions to guide and determine present and future decisions."

It is a bit of a mouthful, but it does embody the core idea of what a policy aims to do: it embodies the *definite* course or method of action selected from among altenatives, considering a given context.

In other words, a policy is an **enforceable guideline** that prescribes a specific course of action when certain conditions are met. The context can vary widely: it might be a city, a country, or even an entire continent. But in our world, it can just as easily refer to an open-source community of developers, an organization, or even a specific security team.

Applied to technology, policies then become enforceable guidelines for decisions and actions within a specific technology stack.
For example, statements like “*use the latest cryptographic algorithms*” or “*ensure that all code has been reviewed by a member of the security team*” are policies.

These kinds of statements fill the **many** pages of organizational policy documents... Those that so often give developers headaches.Yet these same statements define how compliance is maintained against internal controls and external standards, which are critical for any organization.

The question then becomes: how do we simplify this for developers? 
The answer is clear: Let's codify and automate it. 

In fact, what if I told you that most of these rules can be codified?

The central thesis of this blog is simple:

> As long as a policy can be expressed as a decision problem (that is, a yes or no question) and the necessary data is available in a JSON-readable format, that policy can be codified and enforced.


# <a name="opa"></a>Enter, OPA

This is where OPA comes in: it gives you a consistent, programmable layer to evaluate yes-or-no decisions across your stack. 

OPA’s policy language is called Rego, and to explain why policy engines are exciting, we need to talk briefly about *declarative programming*. 

I’ve always loved programming language theory, so I met Prolog, Datalog, Haskell, and OCaml early in my career. All of these languages are declarative. That is, the programmer declares the programmer describes what they want, not how to do it. The operational details (loops, state, control flow) are left to the compiler or runtime. In contrast, *imperative* (or operational) languages like C++, Java, or Python require step-by-step instructions to achieve the same outcome.

It is not just an academic distinction. Declarative languages let you model problems that “common” imperative or OOP languages often make awkward by needing hand-rolled loops, mutable state, or heavy utility libraries.

Take a simple example: *calculate the sum of the squares of the even numbers in a list*.


In C++:

```c++
//cpp
long sumSquaresOfEvens(const std::vector<int>& xs) {
    long acc = 0;
    for (long x : xs) {                
        if ((x & 1) == 0) {
            acc += x * x;
        }
    }
    return acc;
}

```

This works, but you are manually maintaining an accumulator, branching, and being careful about types. 

In a declarative language like haskell:

```haskell
sumSquaresOfEvens :: Integral a => [a] -> a // function constrained to Integrals.  
sumSquaresOfEvens = sum . map (^2) . filter even
```

In a single line, you describe the transformation: keep evens, square them, sum. The compiler takes care of how that happens.

That is the beauty of the declarative style: you express intent, not mechanics.

From there, we reach *logic programming*, where programs are built from logical statements. Think conjunctions, implications, and negations. The program execution then becomes a process of inference.

In Rego (which is based on Datalog), this logic is expressed through [Horn clauses](https://en.wikipedia.org/wiki/Horn_clause): rules that specify the conditions under which a statement holds true. The inference engine (i.e., the OPA binary) queries the data against these clauses. If the data satisfies the rules, the policy permits; otherwise, it denies.

My intent isn’t to go down the rabbit hole of programming paradigms, but if you’re curious about the internals of Rego regarding programming paradigm, [this Snyk blog post](https://snyk.io/articles/getting-started-with-practical-rego/) offers an excellent deep dive.


# <a name="policyopa"></a>From Logic to Policy: How OPA Evaluates Decisions
