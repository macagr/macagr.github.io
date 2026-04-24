---
title: "Open Policy Agent: Highlighting the Policy Bit (Part 1)"
date: 2026-04-24 12:00:00 +0000
categories: [Policy as Code, DevSecOps]
tags: [opa, rego, policy-as-code, compliance, governance]
math: true
image:
  path: /assets/images/OPA_logo.png
  alt: Open Policy Agent logo
---

It is a bit crazy how long it can take me to sit down and write these blogs (it's been 4 years). I will spare you the promises of writing more often (though, truth to be told, I *do want* to start writing more regularly). This is the first part of a series that talks about Open Policy Agent. With that out of the way, let us dig in! 

[Open Policy Agent (or OPA)](https://www.openpolicyagent.org/) is an open source policy engine designed to help with the management of __policies__ across your technology stack, in an attempt to improve security and auditability. 

At its core, the concept is deceptively simple: You have a binary file that receives a `JSON` file, and a `rego` policy (a small code snippet describing what rule to evaluate on that data). The policy engine (OPA binary), then gives you an answer: yes or no. In other words, it tells you whether that `JSON` object satisfies the requirements you have defined in Rego. This decision can be then fed to other services, systems, pipelines or processes that act accordingly. 

Borrowing the usual OPA architecture diagram (from [this Cloud Native Now article](https://cloudnativenow.com/features/introduction-to-open-policy-agent-opa/)):

![OPA evaluating a JSON input against a Rego policy and returning a decision](/assets/images/opa_arch.svg)

We can see that OPA's architecture is simple, but that's precisely what makes it powerful. It is flexible enough to live anywhere in your stack: from CI/CD pipelines to Kubernetes clusters, and from REST APIs to your cloud infrastructure. 

In this blog I will try to dig into the concepts of **policy**, **policy engine**, the **Rego policy language**. We will look at practical examples for both technical readers (with code snippets) and organizational leaders interested in compliance and governance.

Because ultimately, tools like OPA do not just help us write safer code, they also can help organizations to codify their own internal requirements, ensuring trust.


# What is a Policy, Really?

Before going any further, I believe it’s important to clarify the terms we’ll be discussing. Part of this blog is dedicated to answering a simple but essential question: what is a policy?

[The Merriam-Webster dictionary](https://www.merriam-webster.com/dictionary/policy) offers several definitions, but the one I like the most is:

>"a definite course or method of action selected from among alternatives and in light of given conditions to guide and determine present and future decisions."

It is a bit of a mouthful, but it does embody the core idea of what a policy aims to do: it embodies the *definite* course or method of action selected from among alternatives, considering a given context.

In other words, a policy is an **enforceable guideline** that prescribes a specific course of action when certain conditions are met. The context can vary widely: it might be a city, a country, or even an entire continent. But in our world, it can just as easily refer to an open-source community of developers, an organization, or even a specific security team.

Applied to technology, policies then become enforceable guidelines for decisions and actions within a specific technology stack.
For example, statements like “*use the latest cryptographic algorithms*” or “*ensure that all code has been reviewed by a member of the security team*” are policies.

These kinds of statements fill the **many** pages of organizational policy documents... Those that so often give developers headaches. Yet these same statements define how compliance is maintained against internal controls and external standards, which are critical for any organization.

The question then becomes: how do we simplify this for developers? 
The answer is clear: Let's codify and automate it. 

In fact, what if I told you that most of these rules can be codified?

The central thesis of this blog is simple:

> As long as a policy can be expressed as a decision problem (that is, a yes or no question) and the necessary data is available in a JSON-readable format, that policy can be codified and enforced.


# Enter, OPA

This is where OPA comes in: it gives you a consistent, programmable layer to evaluate yes-or-no decisions across your stack. 

OPA’s policy language is called Rego, and to explain why policy engines are exciting, we need to talk briefly about *declarative programming*. 

I’ve always loved programming language theory, so I met Prolog, Datalog, Haskell, and OCaml early in my career. All of these languages are declarative. That is, the programmer describes what they want, not how to do it. The operational details (loops, state, control flow) are left to the compiler or runtime. In contrast, *imperative* (or operational) languages like C++, Java, or Python require step-by-step instructions to achieve the same outcome.

It is not just an academic distinction. Declarative languages let you model problems that “common” imperative or OOP languages often make awkward by needing hand-rolled loops, mutable state, or heavy utility libraries.

Take a simple example: *calculate the sum of the squares of the even numbers in a list*.


In C++:

```cpp
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
sumSquaresOfEvens :: Integral a => [a] -> a -- function constrained to Integrals.  
sumSquaresOfEvens = sum . map (^2) . filter even
```

In a single line, you describe the transformation: keep evens, square them, sum. The compiler takes care of how that happens.

That is the beauty of the declarative style: you express intent, not mechanics.

The Haskell example above is functional declarative: you describe transformations. Its close cousin is logic programming, where programs are built from logical statements: conjunctions, implications, negations. Execution then becomes a process of inference.

In Rego (which is based on Datalog), this logic is expressed through [Horn clauses](https://en.wikipedia.org/wiki/Horn_clause): rules that specify the conditions under which a statement holds true. The inference engine (i.e., the OPA binary) queries the data against these clauses. If the data satisfies the rules, the policy permits; otherwise, it denies.

My intent isn’t to go down the rabbit hole of programming paradigms, but if you’re curious about the internals of Rego regarding programming paradigm, [this Snyk blog post](https://snyk.io/articles/getting-started-with-practical-rego/) offers an excellent deep dive.


# From Logic to Policy: How OPA Evaluates Decisions

Now, the point of this blog is to specify how logic can be used to write and codify policies. For this, Let us just understand a bit more the idea of a Horn clause. Formally, they are written as $$H \leftarrow B_1 \land B_2 \land \cdots \land B_n$$, where $$H$$ is the head (the conclusion) and $$B_1, \ldots, B_n$$ are the body literals (the premises). Rego, the language behind OPA, uses the same concept: if every body literal holds, the head (that is, our policy decision) evaluates to true.

A policy, following our definition, describes an enforceable guideline that prescribes a course of action given certain conditions. This can be easily rewritten as: *if the given conditions hold, then this conclusion follows.* That is the exact shape of a Horn clause and therefore, it is exactly the shape of a Rego rule. 

Every Rego rule, like a Horn clause, has two parts: a **head** and a **body**. The head names the conclusion (the decision or a value we assign), while the body is a list of expressions that must all be true for the head to be true. For example, let's think of a policy from any organizational handbook: 

> *Employees can access their own salary information. Administrators can access anyone's.*

These two sentences declare one policy. In Rego, this could be written as follows. We are using the [Rego playground](https://play.openpolicyagent.org/) to test this.

```rego
package payroll

default allow := false

# Rule 1: users can read their own salary
allow if {
    input.method == "GET"
    input.path == ["salary", input.user]
}

# Rule 2: admins can read anyone's salary
allow if {
    "admin" in input.user_roles[input.user]
}
```

As you can see there are two rules and the policy will evaluate to either false or true. Mapping back to our Horn clause: `allow` is the head ($$H$$), and the expressions inside each `{ ... }` are the body literals ($$B_1, \ldots, B_n$$). The package becomes the namespace; the whole file composes the policy.

The policy above works as follows: 

1. We declare the package name, in this case `payroll`.
2. We add a deny by default. This is better from a policy perspective, because we only codify the situations where the policy is true; it is false in any other case.
3. The first rule says users can read their own salary: `input.path` identifies the resource being requested, `input.user` identifies the caller, and the rule holds when they match.
4. The second rule says that if the user is `admin`, the rule is always true.

The question then becomes what sort of input this rule receives. As I mentioned before, OPA receives a JSON file which represents data, and a rego policy (our code above). Then, the data in our case, could be JSON files of the following shape: 

```json
{
    "method": "GET",
    "path": ["salary", "alice"],
    "user": "alice",
    "user_roles": {
        "alice": ["engineer"],
        "bob": ["admin"]
    }
}
```
which says that alice is trying to read her own salary, or

```json
{
    "method": "GET",
    "path": ["salary", "alice"],
    "user": "bob",
    "user_roles": {
        "alice": ["engineer"],
        "bob": ["admin"]
    }
}
```
which says that Bob is trying to read Alice's salary, but bob is an admin too, or 

```json
{
    "method": "GET",
    "path": ["salary", "bob"],
    "user": "alice",
    "user_roles": {
        "alice": ["engineer"],
        "bob": ["admin"]
    }
}
```

which says that Alice is trying to read Bob's salary, which should not be allowed. If we run the policy with this information in our playground, we will see that the response is true for the first two, while false on the third case. 

In general terms what will happen is that OPA will take the JSON input and will search for variable bindings that satisfy the rule bodies, returning the resulting decision. This is a computation model without hidden state or side effects and it is deterministic, which means that it always produces the same result. 

> The effect is then clear: a two-sentence policy from an HR document
> becomes eleven lines of code, which can be evaluated in microseconds
> by a machine, that a reviewer can read in a pull request and that
> an auditor can trace to a specific control. This is the central
> value of the idea of Policy-as-Code.
{: .prompt-tip }

# Decoupling Decisions from Enforcement

The last idea I would like to introduce before we close Part 1 is the fact that OPA is by design just a decision engine. It will not block an HTTP request, reject a Kubernetes pod, or fail a CI/CD job. It only answers the question with a yes or a no. Something else will act on the answer. Thus, we identify two more components: a **policy decision point (PDP)**, which we mentioned briefly before, and a **policy enforcement point (PEP)**, which then can be an API gateway, an admission controller or a pipeline step. 

This separation allows the same Rego rule to be run in three different places without any modification. This means that the PDP provides one source of truth for different points of enforcement. This makes the model of OPA extremely scalable. 

In Part 2, we will get more hands-on with OPA. We will write richer policies and we will show how we can use OPA to enforce pod admissions in Kubernetes, as well as show how OPA can be used to enforce authorization. 
