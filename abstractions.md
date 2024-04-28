---
date: 2023-09-01
---
# Abstractions
The goal of this document is not to present any brand-new ideas; I'm sure none of the ideas here are new. This is just an exercise to organize my thoughts on the subject.

---

Fundamentally, the job of a software engineer is to **change existing code**. (Unless you're working on a brand-new project.)

According to Michael C. Feathers, there are four principal reasons to change code:

> 1. Adding a feature
> 2. Fixing a bug
> 3. Improving the design
> 4. Optimizing resource usage

If "change" is the primary activity of a software engineer, then we might be interested in how to optimize the speed of code changes.

To do this, we must first understand the mechanics of making a code change - each code change can be broken down into the following steps:
1. Figure out how to make the change
    - Spend time gaining context and understanding of the existing system
    - Read existing code, technical docs, etc.
2. Make the change
3. Verify the accuracy of the change
4. Verify that the change didn't break existing behavior

## Optimizing Step 1
In my experience, one of the primary reasons that step 1 is slow is because of **complexity**.

- Reduce cognitive load

### Eliminate Unnecessary Complexity
- Conciseness
- Dead code
- Unnecessary layers

### Manage Necessary Complexity
- Abstractions (declarative programming)
- Use modules
    - "nested modules" architecture
    - "library" analogy
- If modules are mixed together, then it can be hard to understand and tease apart

## Optimizing Step 2
- Use abstract interfaces
- Each module is a "black box"
- Each black box is composed of smaller black boxes
- Don't depend on concrete implementation details - this makes it harder to swap modules
    - Object.keys() example???
- (Dependency Inversion Principle, D in SOLID)
- Faster re-compile times

## Optimizing Step 3 and 4
- FAST way to verify
- Quick feedback loop
- Tests serve as a sanity check - did we implement the interface properly?
- Makes it easy to swap out concrete implementations
- This works for any level of test (i.e. e2e, acceptance, integration, unit)
- Encourages us to make our code more modular
    - "Good design is testable, and design that isn’t testable is bad." Legacy code book, page 139
