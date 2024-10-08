---
layout: post
title:  "A fictional alternate reality with Functional Computing"
---

## What if computing had started on a functional foundation instead of a procedural one?
---
Early computers were designed around the Von Neumann architecture, which is inherently procedural. The Von Neumann model is based on sequential execution, where a central processing unit (CPU) executes one instruction at a time, manipulating data stored in memory. Imperative languages like Assembly and Fortran closely mirrored this architecture. They allowed programmers to control the flow of execution directly. Also, operations like reading and writing memory, jumping to specific instructions, and modifying data in place were easily expressed with imperative instructions.

This tight coupling between hardware and software made imperative languages a natural fit for early computing. In the early days of computing, hardware was extremely limited. Memory was scarce and expensive. CPUs were slow by modern standards.

Imperative and procedural languages offered low-level control over the machine's resources. Functional programming, by contrast, often involves more abstraction, recursion, and immutability, which are computationally expensive—at least with the hardware available at the time. Imperative languages allowed early programmers to optimize their code to make the best use of available memory and processing power, which was crucial when resources were limited.

The imperative programming model closely aligns with how we naturally think about carrying out instructions: “Do this, then do that.” This kind of step-by-step control matched how early computers worked and how engineers and programmers thought about solving problems, especially problems tied to real-world processes (e.g., running calculations, managing payroll, controlling hardware devices).

Procedures or functions in these early languages were simply ways of grouping steps to be repeated. Still, they didn’t necessarily follow the mathematical notion of a function that returns a value based on its inputs, as is common in functional programming.

Though lambda calculus (the basis of functional programming) was developed in the 1930s, the development of computing was initially driven by practical needs (e.g., performing calculations for wartime efforts, scientific simulations, and business operations). The focus was on getting machines to work and solve real-world problems rather than implementing abstract mathematical principles.

While the formal mathematical theory underlying functional programming existed, it wasn't widely applied to computing in the early days. Researchers and developers were more concerned with immediate practical concerns, like building working compilers for procedural languages that could run on existing hardware.

Functional programming depends on abstractions like higher-order functions, first-class functions, immutability, and recursion. These were more difficult to implement efficiently on early hardware and were conceptually more challenging for early programmers. These ideas also required a shift in thinking from direct memory manipulation and control flow (which procedural languages offered) to more abstract, mathematically rooted ways of structuring programs.

Once languages like Fortran (1957) and Cobol (1959) gained widespread adoption, they became the standard in many industries, and the programming paradigm became entrenched. Even though Lisp (1958) emerged shortly afterward and was highly influential in scientific computing and AI, the widespread industry and governmental investment in procedural languages ensured that they remained dominant for many years.

While imperative languages were designed around the needs and limitations of early hardware, the need for more abstract problem-solving and better ways to reason about programs (without side effects and mutable states) led to the development of functional programming. As computer science evolved, researchers recognized the benefits of mathematical purity, immutability, and more declarative expression of algorithms. This is why languages like Haskell (1990) and ML (1973) emerged, emphasizing these concepts.

### But, what if!

Imagine a world where the foundational principles of computer hardware were built around data flow and functional concepts rather than the sequential, step-by-step execution model of the Von Neumann architecture. This alternate history could radically affect how we think about programming, hardware, and technology.

![Harmony between functional and procedural computing world](../assets/img/functional-and-procedural-harmony.png)

*How things might have unfolded:*

In this alternate history, the earliest computers would have been designed to model data transformations directly rather than instruct execution. Imagine that in the 1930s, Alonzo Church's lambda calculus (which is the mathematical foundation of functional programming) and related theories became the primary driving force behind the first computers instead of the focus on sequential, imperative instructions that we saw with early hardware designs like ENIAC.

Early computers, instead of focusing on step-by-step instruction execution (imperative approach), would focus on evaluating expressions and transforming data. Each "hardware node" would focus on applying a function to an input, transforming data, and passing it along to other nodes. These machines would be built as networks of function processors that map inputs to outputs in a parallel, decentralized way. This data-centric design would lend itself to high degrees of parallelism from the very beginning.

Rather than using memory and CPU as we know them today, the foundational hardware units would be modeled after functional units that inherently operate without side effects. Think of a grid of processors, each performing a transformation on its inputs and sending the results to the next unit. This model would mirror data flow architectures seen in some advanced computing systems today, but it would be the default in this world.

The first programming languages would be built to match the functional, data-centric nature of the hardware. Instead of early languages like Assembly or Fortran, we would have had a language based on functional abstractions. The first programming language might have be a precursor to something like Lisp or Haskell, but it was designed to operate seamlessly on the hardware from the start. Programming would focus on defining pure functions that map inputs to outputs. Recursion and higher-order functions (which take other functions as arguments) would be commonplace initially.

These languages would inherently avoid side effects, state mutation, or memory manipulation because the hardware would be structured to discourage these practices. Programmers would consider data transformations and compositionality, where complex operations are built by combining simpler ones.

One of the critical advantages of functional programming is the ease of parallel execution due to the lack of side effects and mutable state. In this alternate timeline, early machines would be capable of massive parallelism because functional units can compute results independently of each other as long as they have the required inputs. This would have likely pushed advancements in parallel computing and multi-core architectures decades earlier. Instead of the gradual adoption of parallel processing in modern CPUs, it would have been a foundational computing element even from the very beginning.

These machines might have used something akin to data flow graphs, where computations would be visualized as a network of transformations that occur simultaneously as data "flows" through the system. This would represent an entirely different approach to computation, one that doesn’t rely on manipulating program counters, registers, and memory but instead evaluates expressions concurrently.

Memory systems in this world would be based on the concept of immutability. Since functional programming heavily emphasizes immutability (where data, once created, cannot be changed), the hardware would reflect this. Memory cells might have worked like persistent data structures, where new data was stored in a new location, and existing data was preserved without modification. This would have created a sharp contrast between how memory works today and how data is routinely overwritten. Early systems would likely optimize for garbage collection and efficient memory reuse, techniques that functional languages need to manage immutable data.

In this world, algorithms that depend on mutable states, such as imperative loops and direct memory access, would be foreign concepts. Instead, all algorithms would revolve around recursive definitions and pure data transformations.

Without a concept of a global state, programs would be designed to avoid side effects entirely. Side effects (like modifying global variables or performing I/O operations) is often a necessary evil, but in this fictional history, everything would be purely functional. Input-output (I/O) would be modeled as a pure function that transforms a state of the world into a new state. For example, instead of writing directly to a file, a function would take the current state of a file system and produce a new version with the desired changes. This would require very different thinking about I/O and external systems, such as networking and storage. Concurrency would have been mastered much earlier because side effects (which lead to race conditions and synchronization issues) would be much rarer, thanks to the inherent nature of functional programming.

Functional programming languages like Lisp (or its equivalent) would still dominate the early AI and symbolic computing fields. However, given the overall focus on functional machines, we might have seen an accelerated development of AI and machine learning algorithms based on data transformations and functional pipelines.

AI models might have been built with purely functional neural networks, where layers of neurons were modeled as mathematical functions. Due to the functional hardware, these systems would be easier to parallelize, leading to earlier breakthroughs in neural networks and AI systems.

In our fictional world, software interfaces would be highly declarative. Modern examples like React (for building UIs declaratively) or SQL (for querying databases declaratively) would not be later inventions—they would be natural developments from the very first systems. User interfaces might be built with expressions like “Show this data” instead of defining a step-by-step algorithm for rendering. User interactions would be modeled as functional transformations of a user’s inputs and system state, with no need for global event handlers or imperative control over components.

Without the hardware efficiency challenges of our world’s imperative paradigm, industries would have adopted functional programming from the start. Businesses and engineers would have been trained to think functionally and understand complex systems as data flow compositions rather than step-by-step instructions. It might have led to cleaner, more maintainable codebases in software engineering, as avoiding side effects and global state would naturally encourage more modular and testable programs.

In a functional-hardware world, quantum computing and distributed systems would emerge more naturally. Functional programming’s emphasis on immutability and statelessness makes it a better fit for distributed computing (such as serverless architectures). It could allow for the easier adoption of quantum principles in computing, where the concept of state and side effects is fundamentally different from classical computing.

The world of computing would have developed very differently. Instead of focusing on control flow and state manipulation, everything would revolve around data transformations and pure functions. The inherent parallelism of functional programming would push the boundaries of computing much faster, and the entire software and hardware landscape would be centered around clean, declarative, and compositional systems from the very beginning. Programming would be more mathematically grounded, cleaner, and potentially easier to reason about, but it would also require a very different mindset for thinking about how machines work.

This alternate world might, very likely, have produced more scalable, modular, and parallel systems from the outset, potentially accelerating advancements in certain fields like AI, distributed computing, and declarative systems. However, the abstract nature of functional programming might, equal certainly, have presented its own set of challenges for practical engineering.

Procedural programming would have likely emerge as a complement to functional programming, filling gaps where state management, performance, or low-level control are critical. However, the mindset of developers would still be shaped by the functional-first principles! Procedural code would be modular, testable, and used sparingly, likely in niche domains like low-level systems programming, real-time systems, and stateful user interfaces. The dominance of functional programming would lead to cleaner, more maintainable codebases overall, with procedural programming being a domain-specific tool rather than the default paradigm.

This balance between functional and procedural approaches would have lead to fewer problems with untestable, state-heavy code, while still allowing the flexibility to optimize performance and control when necessary. In essence, procedural programming would evolve to address the specific limitations of functional programming, much like functional programming today exists to solve the problems inherent in imperative paradigms.