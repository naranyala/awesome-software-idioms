# Awesome Software Idioms

> A catalog of idioms, phrases, principles, mentalities, and philosophies to adopt, share, and discuss.
> Universal first, language-specific examples second. Every entry is one line of definition, one line of *so what*, and a pointer if you want the deep dive.

This is a **living document**. If an entry doesn't earn its place — if you can't explain it to a junior in one breath and defend it in a design review — cut it. The list is opinionated on purpose: there is no neutral "best practice," only tradeoffs you've chosen to name.

---

## Table of Contents

- [How to Use This List](#how-to-use-this-list)
- [Part I — Foundational Axioms](#part-i--foundational-axioms)
  - [Complexity & Simplicity](#complexity--simplicity)
  - [Abstraction & Design](#abstraction--design)
  - [Coupling, Cohesion, Boundaries](#coupling-cohesion-boundaries)
  - [Names as Documentation](#names-as-documentation)
  - [State, Time, and Ordering](#state-time-and-ordering)
  - [Error Handling](#error-handling)
  - [Interfaces, Contracts, Boundaries](#interfaces-contracts-boundaries)
- [Part II — Engineering Practices](#part-ii--engineering-practices)
  - [Testing](#testing)
  - [Version Control & Collaboration](#version-control--collaboration)
  - [Refactoring](#refactoring)
  - [Code Review](#code-review)
  - [Documentation](#documentation)
- [Part III — Design Principles and Patterns](#part-iii--design-principles-and-patterns)
  - [SOLID](#solid)
  - [Other Named Principles](#other-named-principles)
  - [Classic Gang of Four Patterns](#classic-gang-of-four-patterns)
  - [Patterns People Misuse](#patterns-people-misuse)
  - [Anti-Patterns](#anti-patterns)
- [Part IV — Architecture Philosophies](#part-iv--architecture-philosophies)
  - [Unix Philosophy](#unix-philosophy)
  - [Functional & Declarative](#functional--declarative)
  - [Object-Oriented](#object-oriented)
  - [Distributed Systems](#distributed-systems)
  - [Data-Centric Thinking](#data-centric-thinking)
- [Part V — Language & Runtime Idioms](#part-v--language--runtime-idioms)
  - [C / C++](#c--c)
  - [Go](#go)
  - [Rust](#rust)
  - [Python](#python)
  - [JavaScript & TypeScript](#javascript--typescript)
  - [Java & JVM](#java--jvm)
  - [SQL & Databases](#sql--databases)
  - [Shell & Automation](#shell--automation)
- [Part VI — Performance & Scale](#part-vi--performance--scale)
- [Part VII — Security Mindset](#part-vii--security-mindset)
- [Part VIII — Team & Process Mentalities](#part-viii--team--process-mentalities)
  - [Agile Flavors](#agile-flavors)
  - [Technical Debt](#technical-debt)
  - [Estimation & Deadlines](#estimation--deadlines)
  - [On-call, Support, and Ownership](#on-call-support-and-ownership)
  - [Product & User Empathy](#product--user-empathy)
- [Part IX — Software as a Craft](#part-ix--software-as-a-craft)
  - [Wisdom, Proverbs, Aphorisms](#wisdom-proverbs-aphorisms)
  - [Anti-Wisdom](#anti-wisdom)
  - [Learning & Growth](#learning--growth)
  - [Ethics](#ethics)
- [Part X — The Canon: Primary Sources](#part-x--the-canon-primary-sources)
- [Verifying Quotes and Claims](#verifying-quotes-and-claims)
- [Contributing](#contributing)

---

## How to Use This List

**Three ways in, depending on why you're here:**

1. **Onboarding** — read Part I and Part IX. That is the load-bearing 10%.
2. **Design review** — jump to the section for the decision in front of you. Name the tradeoff out loud before you pick a side.
3. **Team ritual** — pick one entry per week. Read it, argue about it in standup, decide whether your codebase already obeys or violates it, and write down the verdict in an ADR. That last step is the one that makes it stick.

**A note on false balance.** Some entries below are genuinely contested — "microservices," "event sourcing," "TDD." Others are close to settled — "don't commit secrets," "measure before optimizing." The markers below tell you which is which:

| Marker | Meaning |
|---|---|
| ⚖️ | **Contested.** Reasonable people disagree. Argue it out. |
| ✅ | **Broadly settled.** Deviating requires a stated reason. |
| 🔍 | **Tool/technique**, not a principle. Useful, contextual, eras. |
| 💬 | **A phrase worth saying out loud** in a design review. |

---

## Part I — Foundational Axioms

### Complexity & Simplicity

- ✅ **Occam's Razor / Minimum Solution** — Solve the problem that exists, in the fewest concepts that fully solve it. *So what:* every abstraction is a permanent maintenance liability you must pay rent on.
- ✅ **KISS — Keep It Simple, Stupid** — Simplicity is a feature, not a compromise. *So what:* "it's more general" is not an argument for shipping something complex today.
- ⚖️ **YAGNI — You Aren't Gonna Need It** — Don't build for imagined futures. *Counter-argument:* in some domains you *are* going to need it, and the cost of retrofitting is catastrophic (protocols, schemas, public APIs). The real lesson: YAGNI is about *capability*, not about *flexibility*.
- ⚖️ **Principle of Least Astonishment** — The system should behave the way a competent practitioner of its language expects. Surprise is a bug, even when it's "clever." *So what:* read the ecosystem's conventions before you invent your own.
- ✅ **The Pit of Success** — Design so the easy path is also the right path. If the correct usage is the laborious one, the design has failed. *(Object-C's `init` hierarchy, Rust's ownership-by-default, Go's zero-value usefulness.)*
- 💬 **"It's not complicated, it's just not written down yet."** — Often true. A wall of tangled code is usually a missing model, not a hard problem.
- 🔍 **Flyweight** — Store shared immutable state, pass around references. Cheap objects over duplicated state.
- 🔍 **YAGNI-adjacent: Rule of Three** — Abstract after the *third* real instance. One is a coincidence, two is a pattern, three is a design.

### Abstraction & Design

- ✅ **The Law of Demeter / Principle of Least Knowledge** — A method may only touch its own object's data and the data of objects handed directly to it. No `a.getB().getC().setD()`. *So what:* change `C` internals without hunting for every three-hop chain.
- ✅ **Principle of Least Privilege** — Every component, key, and token gets the minimum access it needs, for the minimum time. *So what:* the blast radius of any single compromise shrinks to something survivable.
- ✅ **The Principle of Locality / Information Hiding** — Hide the decisions most likely to change behind stable interfaces, and keep them together. *(Parnas, 1972 — "On the Criteria To Be Used in Decomposing Systems into Modules." This is arguably the most important idea in the list.)*
- ⚖️ **DRY — Don't Repeat Yourself** — Every piece of knowledge has one authoritative expression. *Nuance:* DRY is about *knowledge*, not text. Two coincidentally-identical functions encoding *different* reasons are fine; one regex encoding six different rules is not.
- ✅ **Rule of Three** (again, worth twice) — Wait for the third repetition before you abstract.
- 🔍 **The Rule of Three, Empirical Form** — Glass's Fact #18 gives the reuse version of this, and it's the best available evidence for the heuristic: there are *two* rules of three in reuse — (a) it is **three times as difficult** to build a reusable component as a single-use one, and (b) a component should be tried in **three different applications** before it's general enough to belong in a reuse library. *(Robert Glass, *Facts and Fallacies of Software Engineering*, 2002, Fact #18.)* Note that (b) means the classic "third repetition" rule is a *lower bound* for a production abstraction, not a green light.
- 🔍 **Composition Over Inheritance** — Prefer composing behavior to subclassing behavior. *So what:* avoids fragile base-class taxonomies where a subclass changes you didn't predict. *(Effective Java, Item 18.)*
- 🔍 **Liskov Substitution Principle** — If `Dog` is substitutable for `Animal`, it must truly be one. Violations (notably mutable collections as `List<T>`) are where type systems stop helping.
- 💬 **"Make illegal states unrepresentable."** — The best type system is the one that deletes a class of bugs rather than documenting it. *(The phrasing is usually credited to Erik Meijer / the Haskell and ML community, and popularized through Yulii's talk; treat the attribution as loose. The *idea* is not: model a domain as a sum type and half the bug reports stop existing.)*

### Coupling, Cohesion, Boundaries

- ✅ **High Cohesion, Low Coupling** — Modules that do one job; modules that need to know little about each other.
- ✅ **Design for Replacement** — Assume every dependency will be swapped, forked, or replaced within 18 months. It usually is. This is why dependency injection and thin interfaces exist.
- ⚖️ **Distributed Monolith / Microservices** — Splitting a codebase across the network is a real tradeoff, not a free win. You trade in-process calls for network failure, distributed transactions, deploy coupling, and observability debt. *Ask:* would a module boundary in one process with a hard interface check satisfy the actual reason for splitting? (Team autonomy, independent deploys, scaling profile, compliance boundary — pick one reason.)
- ✅ **Parse, don't validate** — Data at the boundary becomes a rich typed structure, not a bag of strings you re-check everywhere. *So what:* you can then make invalid states unrepresentable, and you validate once, at the edge.
- 🔍 **Anti-Corruption Layer** — Wrap a foreign/legacy model so its weirdness stops leaking into your domain.

### Names as Documentation

- ✅ **Names Should Say What, Not How** — `getActiveCustomersByRegionSince` beats `queryData()`. *Rule of thumb:* if you need a comment to explain a name, the name is wrong.
- ✅ **Avoid Synonyms and Homonyms** — If you have `Manager`, `Handler`, `Controller`, and `Service`, you've created four meaningless words. Pick one and use it everywhere. *So what:* a reader who knows one knows all four.
- ✅ **Say the Thing** — A name that says what the thing *is* rather than what it *does* ages better. (Kent's "The Name of the Thing.")
- ✅ **Don't Over-Word** — `User` beats `UserObject` and `IUser`. Prefixes that describe a category (`I`, `Impl`, `Base`, `Abstract`) usually mean the naming is still a category the reader must learn.
- 💬 **"If the name is wrong, you are not allowed to write the class."** *(Sandi Metz, paraphrasing.)*
- 🔍 **Boolean names should read as predicates** — `isActive`, `hasAccess`, `canRetry` — so `if (x)` sites read as English sentences.

### State, Time, and Ordering

- ⚖️ **Make Illegal States Unrepresentable** — Design types so that `Email | UnverifiedEmail` isn't a thing you can accidentally construct. *(Algebraic Data Types, type-state patterns.)*
- ✅ **Immutability by Default** — Objects that don't change can't be corrupted, can't be observed mid-update, and are free to share. *(Persistent data structures in Haskell, Kotlin, Clojure, Elixir; Rust's ownership is immutability-by-scope.)*
- ✅ **The Pit of Mutable Shared State** — The #1 source of heisenbugs, race conditions, and "works on my machine." Pass data; transform it; return new data.
- ✅ **Single Source of Truth (SSOT)** — One authoritative home for each fact. Derived data is derived, on demand, never stored twice. *So what:* eliminates a whole class of sync bugs.
- ✅ **Idempotency** — Operations designed to be safely retried. Because they *will* be retried: timeouts happen and you can't distinguish "never ran" from "ran but the response was lost." *Practical consequence:* idempotency keys, dedup on delivery, UPSERTs.
- ✅ **Time is the Hardest Part** — Time zones, DST, leap seconds, clock skew, and monotonic-vs-wall-clock are not edge cases. Store instants in UTC, store durations as numbers, and never do date math on strings. *(Joel Spolsky, "The Death of Date".)*
- ✅ **Ordering and Delivery Semantics Are Assumptions** — "Exactly once" doesn't exist end-to-end. It becomes at-least-once + idempotency, or at-most-once + explicit gaps. *Say which one your system is, out loud, on the architecture diagram.*
- 🔍 **Copy-on-Write / Persistent Data Structures** — Structural sharing makes "new version" cheap.
- 🔍 **Seam for Time** — Inject a clock. Don't call `now()` deep inside logic you want to test.

### Error Handling

- ✅ **Fail Fast** — Detect and report errors at the point they occur, not three layers later with corrupted context. *This is the core argument for validation at construction/parse time.*
- ⚖️ **Exceptions vs. Result/Either Types** — Exceptions are invisible in the signature and convenient; Result/Either are explicit and composable. **Both are right** — use exceptions at boundaries, typed results in domain logic. *(Rust's `Result`; Scala's `Either`.)*
- ✅ **Don't Swallow Errors** — An empty `catch {}` converts a crash into silent corruption. If you can't handle it, propagate it with context.
- ✅ **Error Messages Are for the Operator** — Include what failed, the identifier involved, and the next action. The user sees the operation; the on-call engineer sees the message.
- ✅ **Context-Rich Propagation** — Add information as an error travels up: `fmt.Errorf("load order %s: %w", id, err)`. *So what:* the difference between a 5-minute and a 5-hour incident is usually one good error message.
- ⚖️ **Exceptions as Control Flow (Scala, Clojure, Elixir)** — Powerful when genuinely exceptional, misused when it becomes your primary branching mechanism. The line: is this *expected*?
- ✅ **Retry with Backoff and Jitter** — Never retry immediately in a tight loop; you turn a blip into an outage. *(Google SRE Workbook ch. 22.)*
- 🔍 **Circuit Breaker** — Stop calling something that's down so you don't die with it.
- ✅ **The Twelve-Factor App** — Twelve criteria for building portable, scalable, deployable services. The parts that repay reading: **config is in the environment** (never in code), **backing services are attachable resources** (the database is a resource you swap, not a hard dependency), **disposability** (processes must be crashable and cheap to start), and **dev/prod parity**. (Adams, 2011, 12factor.net.) *Useful as a checklist and excellent as a vocabulary* — "that's not a backing service, that's a hard dependency" is a real design review line.
- 🔍 **Failover vs. Fallback** — Know which one you built, and whether the fallback is genuinely correct or just a different wrong answer.

### Interfaces, Contracts, Boundaries

- ✅ **Program to Interfaces, Not Implementations** — Depend on the contract, not the machinery behind it. *(Go proverb: "Accept interfaces, return structs.")*
- ✅ **Principle of Explicit Contracts** — A function's contract — preconditions, postconditions, side effects — is part of its name, type signature, and documentation. If it has a side effect, say so: `save`, `write`, `flush`, `invalidate`, `close`.
- ✅ **Tell, Don't Ask** — Ask an object to do something rather than interrogating its data and reassembling its behavior elsewhere. *So what:* behavior stays with the data that has it.
- ✅ **Null is a Smell; use absence deliberately** — Null, `None`, `nil`, `undefined`, and empty collection mean four different things. Be explicit about which.
- ⚖️ **Parsing vs. Consuming** — Consume streams incrementally and as you go; parse into a structure to analyze repeatedly. *So what:* decides whether a 2 GB file is a memory leak or a 30-second job.
- ✅ **The Boundary Is Where You Validate** — Everything crossing a process/network/user boundary is untrusted. Parse it into a trusted type immediately, and let the rest of the system assume well-formedness.

---

## Part II — Engineering Practices

### Testing

- ✅ **Test Behavior, Not Implementation** — Tests coupled to internals break on every refactor and resist change. Test what the thing *does*, from the caller's perspective. *(Kent Beck, "Implementation Driven Development is Dead"; Michael Feathers, "Working Effectively with Legacy Code".)*
- ✅ **Test Pyramid** — Many fast unit tests, fewer integration tests, a few end-to-end tests. *So what:* the E2E suite is your most valuable and most expensive safety net; spend it on the top few journeys only.
- ✅ **TDD: Red, Green, Refactor** — Watch the test fail for the right reason, make it pass, then clean up with the test as your safety net. *The hidden benefit isn't coverage — it's that the test is written before the design ossifies.*
- ✅ **Arrange, Act, Assert** — Three distinct phases. When they blur, tests become unreadable and can't be trusted.
- ✅ **One Reason to Fail** — A test asserting ten things tells you nothing when it goes red. Assert one behavior per test; the *arrange* can be rich.
- ✅ **Test the Contract, Not the Current Behavior** — Especially for the behavior you consider ugly but must preserve: those are your refactoring safety net. (Characterization tests, Michael Feathers.)
- ✅ **The Test Pyramid Inverts for Legacy Code** — In a legacy codebase, build a safety net *first* before touching anything. That's the whole game. Cover at the seams (Michael Feathers' seams technique).
- ✅ **Make the Hard to Test Impossible** — If a thing is hard to test, that's usually a design smell: hidden state, hidden time, hidden network, hidden globals. The test difficulty is the signal.
- ✅ **Deterministic Tests** — No reliance on wall-clock time, real network, execution order, or test pollution. If it flakes, it's not a test, it's a coin flip.
- ✅ **Test Data Should Be Obvious** — Use builders/factories for the boring parts, name the one interesting value. Reviewers should see what matters.
- ✅ **Coverage Is a Means, Not a Goal** — 100% line coverage can coexist with zero tested behavior. Cover *what could hurt*, especially money, auth, and data loss.
- ⚖️ **Mock at the Edges, Not the Internals** — Mocking your own collaborators is a design smell; mocking the network/clock/filesystem is a testing tool. *Rule:* if you can't test without mocking, ask what got coupled to what.
- 🔍 **Golden/Snapshot Testing** — Great for stable, large structures. Useless if you regenerate snapshots without reviewing diffs.
- 🔍 **Property-Based Testing** — Hypothesis, fast-check, proptest. Find edge cases you didn't think of by stating invariants instead of examples.
- 🔍 **Metamorphic Testing** — Test *relations* between inputs (shuffle-invariant, add-one-invariant). Works where examples are hard to write.
- 🔍 **Mutation Testing** — Verify your tests actually catch bugs by deliberately breaking the code. The highest-signal use of coverage data.
- 💬 **"Test doubles are a design smell, except at the edges."** *(Sandi Metz.)*
- 💬 **"The best time to write a test was before; the second best time is now, to enable the refactor you're about to do."**

### Version Control & Collaboration

- ✅ **Small, Focused Commits** — One commit, one logical change, one message explaining *why*. *So what:* `git bisect` and `revert` work; reviews are reviewable; the history is documentation.
- ✅ **Write Commit Messages for the Future You** — The future you is a stranger with no context and full write access. Explain the motivation, not the diff (the diff is already there).
- ✅ **Don't Commit Generated/Vendor/Build Output** — Except when the output *is* the deliverable (checked-in lockfiles are a deliberate exception; they're a feature).
- ✅ **Never Force-Push Shared Branches** — `git push --force-with-lease` on your own topic branch is fine; on `main` it is an act of war.
- ✅ **Rebase Your Own Work, Merge Shared Work** — Keeps shared history honest and linear.
- ✅ **Branches Are for Coordinating Humans** — Branch-per-feature is a communication mechanism, not a sacred rite. Trunk-based with small batches works too.
- ✅ **The Golden Rule of Version Control** — Don't rewrite other people's history. Ever. Even if you think you're fixing a mess.
- ✅ **Keep the History Clean but Not Holy** — `git filter-branch` and friends are for history rewrites with a clear, rare justification — removing a leaked secret, not tidying.
- ⚖️ **Feature Branches vs. Trunk-Based** — Trunk-based (short-lived branches, frequent integration, strong CI) generally wins on integration cost; feature branches win on review depth and incomplete work. Pair it with *test what you claim to test* in CI.
- 🔍 **`git bisect`, `git blame`, `git log -S`** — Your three archaeology tools. `git log -S` (pickaxe) is the one people miss: find *when* a string appeared or disappeared.
- 🔍 **Bisect Faster** — Automated `git bisect run` with a script, even a crude one, beats manual bisection. For performance regressions, bisect a benchmark.
- 💬 **"A clean history is a sign of a clean mind."** *(Attributed, variously, to a lot of people.)*

### Refactoring

- ✅ **Refactoring Changes Structure, Not Behavior** — Same tests pass before and after. If behavior changes, it's a feature or a bugfix, not a refactor.
- ✅ **Two Hats** — Martin Fowler: wear the *refactoring* hat (no behavior change) or the *functional* hat (behavior change, no cleanup), never both at once. *So what:* the tangled commit that does both is unreviewable and unrevertable.
- ✅ **Refactor in Small Steps, Commit Often** — Each step is green, reviewable, and revertable. The final state may be months away; the intermediate states must be safe.
- ✅ **Boy Scout Rule** — Leave every module cleaner than you found it. Not a grand refactor — one thing, on every visit, forever. *This is the single most effective maintenance habit.*
- ✅ **The Compiler Is Your Refactoring Assistant** — Rename-anything, extract-function, inline, and safe type changes are the *easy* refactorings. Do them constantly; they cost nothing and keep the code close to its original intent.
- ⚖️ **Technical Debt Is a Loan, Not a Sin** — Fowler: debt is *deliberate* taking a shortcut for a reason, usually to hit a date. Unprincipled messiness is not debt, it's just mess. Different problems, different responses.
- ✅ **YAGNI vs. Debt** — Deleting unused code is not debt repayment; it *is* the repayment. Dead code has ongoing interest and zero principal.
- 💬 **"Delete is the most underused refactoring."** — If a flag, class, or config option has only one value in practice, it's not a feature, it's a branch you have to test forever.
- 💬 **"Don't refactor code you're about to delete."**

### Code Review

- ✅ **Review the Change, Ask the Question** — "Why this approach?" is worth more than "nit: rename this." Style is a linter's job, not a reviewer's.
- ✅ **Review the Diff in Context** — Read the file, not just the hunk. The bugs are almost never inside the changed lines; they're in the assumptions the changed lines make.
- ✅ **Review for Correctness, Then Design, Then Style** — In that order. Approving the right thing in the wrong shape is a wasted conversation; approving the wrong thing politely is worse.
- ✅ **Review Latency is a Feature** — Respond within a day. A review queue is a deployment blocker. (See "Stop the Line.")
- ✅ **Praise in Public, Criticize in Private** — And prefer critique *of the code* to critique *of the person*, always.
- ✅ **Automate the Mechanical** — Formatting, import order, lint rules. Every review comment a bot could have made is a review comment a human didn't have to spend attention.
- ✅ **Review Is Not Approval Of the Author** — You're signing off on the change, not on your colleague's judgment. "LGTM" is a technical statement, not a relationship.
- ✅ **Rubber-Stamping Is Worse Than No Review** — A review that hasn't read the diff actively destroys the signal everyone relies on. If you can't review it, say so and don't approve.
- 💬 **"Review the code, not the person. The code didn't choose to be written."**

### Documentation

- ✅ **The Best Documentation Is the Code** — Clear names and types answer most questions. Reach for a comment only to explain *why*, or to note a constraint the reader cannot see.
- ✅ **Explain the Why, Not the What** — The *what* is in the diff. The *why* evaporates from the codebase the moment the PR merges, unless you wrote it down.
- ✅ **Comments Rot; ADRs Persist** — Architecture Decision Records — the decision, the options considered, the reason, the date — are the highest-value documentation a team can produce, and they take 15 minutes.
- ✅ **Document the Invariant, Not the Implementation** — "This must stay sorted by `effective_date`" survives refactors. "We use a bubble sort here" does not.
- ⚖️ **README as Entry Point vs. Living Docs** — A README that tries to be everything becomes nothing. Keep it a map; put depth in linked documents that are allowed to go stale gracefully.
- ✅ **Document the Errors, Not Just the Successes** — The most valuable doc section is "here's what this does when things go wrong."
- 💬 **"Only comment the parts you can't make obvious."** *(Rob Pike; also Jeff Erlanger's "Code should read like prose.")*

---

## Part III — Design Principles and Patterns

### SOLID

Robert C. Martin's five principles. Widely cited, frequently misapplied. Honest take: **SRP and ISP are the high-value ones in practice; OCP is the most often-mangled** (it gets cargo-culted into a "no `if` statements" rule that produces worse code).

- ✅ **S — Single Responsibility Principle** — A module has one reason to change. *So what:* the reason is a *human* concern, not "does one thing." Ask "which single stakeholder's requirement does this serve?" (Robert Martin.)
- ✅ **O — Open/Closed Principle** — Open for extension, closed for modification. *Practical reading:* add behavior by adding code, not by editing and retesting existing code.
- ⚖️ **L — Liskov Substitution Principle** — Subtypes must be usable anywhere the base type is, without the caller knowing. *The famous violation:* allowing a `Stack`'s `push` to break the LIFO order inherited from `List`.
- ✅ **I — Interface Segregation Principle** — Prefer small, specific interfaces over one fat one. *So what:* clients stop depending on methods they don't use, so a fat interface can never evolve freely.
- ✅ **D — Dependency Inversion Principle** — Depend on abstractions, and the *detail* should depend on the abstraction, not the other way around. *This is the principle that makes testing, pluggability, and framework independence possible.*

### Other Named Principles

- ✅ **Principle of Least Knowledge / Law of Demeter** — See Part I. One dot per method. (Karl Hoehn, The apgraphics article, 1989.)
- ⚖️ **The Principle of Command Query Separation** — Commands change state and return nothing meaningful; queries return data and change nothing. (Bertrand Meyer.) Following it makes a shocking number of bugs impossible; ignoring it is a real source of "phantom update" bugs.
- ✅ **Principle of Expressive Code** — The code should express the intent directly, in one obvious way. (Jacques Carel.) If you need a comment, the code failed. *(The book of that name has practical recipes; e.g. "assert instead of if-then-throw".)*
- ✅ **The Principle of Proximity** — Related things should live together, and the closer the more related. (The Art of Readable Code.)
- ✅ **Reuse at the Right Altitude** — Reuse a *concept* when it's stable and shared; write it out when it *varies* on purpose. (Sandi Metz.) Three copies of similar-but-deliberately-divergent code is often better than one over-parameterized abstraction.
- ✅ **Principle of Least Knowledge Applies to APIs Too** — Don't leak the internal representation. Return `List<LogEntry>`, not a mutable internal list; return a value object, not raw fields.
- 🔍 **Acyclic Dependencies Principle** — Your package/module graph must be a DAG. *This is* the practical, checkable form of a lot of "good architecture."
- 🔍 **Stable Dependencies Principle** — Depend on things that change less often than you do. (Robert C. Martin.) A quick way to spot bad coupling.
- ✅ **Deep Modules** — A module with a *simple* interface hiding *substantial* functionality. The cost of a module is its interface; its benefit is its functionality. Shallow modules (thin wrappers, pass-through layers, "manager" classes that only forward calls) add interface cost with no payoff. *This reframes SRP usefully:* "do one thing" should mean "do one thing **fully**," not "be small." (John Ousterhout, *A Philosophy of Software Design*, 2018.)
- ⚖️ **Strategic vs. Tactical Programming** — Tactical: get something working as fast as possible. Strategic: think about the design, then build. Ousterhout's warning is that tactical programming is a one-way door — it's very hard to switch to strategic later, because the mess is already load-bearing. His sharpest phrase for the person who embodies the failure mode: the **"tactical tornado"** — fast, effective in the moment, and leaves a wake of destruction for everyone else.
- ✅ **Design It Twice** — Your first design is rarely your best. Generate two or three genuinely different approaches, compare them, then pick. Ousterhout's own Tk toolkit API was a direct application of this. *Cheap relative to a rewrite, and the single highest-leverage habit in his book.*
- ✅ **Define Errors Out of Existence** — The best way to handle an error is to design so it cannot occur. Prefer a total function over a partial one; prefer an impossible state over a handled one. (Ousterhout, *APOSD* ch. 8.) ⚠️ *Caveat he himself flags:* this is not a license to delete necessary error checks — deleting a check without removing the possibility is just hiding the bug.
- ✅ **Different Layer, Different Abstraction** — A file parser and a UI should not share an abstraction just because they both touch files. Forcing shared abstractions across layers ("pass-through" methods to make things look uniform) is a common source of shallow-module proliferation.
- ✅ **Pull Complexity Downward** — Push details into lower-level modules so higher layers read as clean intent. Also: **general-purpose** modules are usually deeper than special-purpose ones, because they amortize design effort across more uses. (Ousterhout, ch. 6 — notably expanded in the 2nd edition, 2021.)
- ⚖️ **Method Length: Clean Code vs. Ousterhout** — Robert Martin: "functions should do one thing," short methods, extract aggressively. Ousterhout: this is taken too far and produces fragmented code that's hard to follow; a long, clearly-written method that does one coherent job is fine. **Both are right about the goal (clarity) and disagree about the lever.** Practical resolution: judge methods by whether reading them top-to-bottom tells a coherent story, not by line count. (Ousterhout addresses this disagreement head-on in the 2nd edition.)
- ✅ **Passphrase: "The greatest limitation in writing software is our ability to understand the system."** — Ousterhout, *APOSD* ch. 1. *Why it matters:* it reframes complexity management as a human-cognitive problem, which is why a change that is "obviously simple to the author" can be incomprehensible to everyone else.

### Classic Gang of Four Patterns

Patterns are **solutions with names**, so you can say "this is a Strategy" instead of re-explaining it. They're a shared vocabulary, not a checklist. Rule of thumb from Fowler: **patterns are for people who have already solved the problem and want to discuss it** — if you're reaching for a pattern before you understand the problem, you probably need a simpler answer.

- 🔍 **Strategy** — Swap interchangeable behavior behind a common interface. `(collection.sort(|a,b| ...))`, `formatting options`.
- 🔍 **Adapter** — Make an incompatible interface fit yours. `(YourAppCode ⇄ LegacyPayoutSystem)`.
- 🔍 **Facade** — One simple entry point over a complicated subsystem. *(Generally good. Also the tool of choice for hiding a vendor SDK.)*
- 🔍 **Decorator / Wrapper** — Add behavior by composition, at runtime, in a stack. *(The single most useful pattern in logging, I/O, and retry.)*
- 🔍 **Factory / Abstract Factory** — Centralize "how do I get one of these," hide the construction details. *(Go proverb: "A little copying is better than a little dependency.")*
- 🔍 **Observer / Publisher-Subscriber** — Notify many dependents of a change, without coupling them to the notifier. ⚠️ Beware: the async version silently turns your system into a distributed system with a `bus` in the middle.
- 🔍 **Singleton** — One instance, globally accessible. ⚠️ Almost always a worse idea than dependency injection. The `global` keyword in Go exists for a reason, and the discussion about it is instructive.
- 🔍 **Builder** — Assemble a complex thing readably. (Go proverb: "If you have a type with a `+` method, use `+`." → Go's functional-option pattern.)
- 🔍 **Repository** — A collection-like interface over persistence. ⚠️ Often over-applied: see below.
- 🔍 **Iterator / Generator** — Traverse a collection without knowing its representation. *(Present in every language; often the right answer to "how do I return an unknown-size result".)*
- 🔍 **Template Method vs. Composition** — Inheritance-based vs. composition-based. Almost always prefer composition; that's the whole point of the reframe.
- 🔍 **Proxy / Decorator for Remote** — Local stand-in for a remote thing. (gRPC stubs, `HttpClient`.)

### Patterns People Misuse

- ⚖️ **Repository Pattern** — A generic CRUD interface over a database is often a *worse* anti-pattern: it hides the database, hiding its strengths, and produces a second query language that's easier to get wrong. *Prefer:* query directly for reads; use repositories when the domain genuinely needs to abstract storage (multi-backend, complex aggregates, testing).
- ⚖️ **Event Sourcing** — Append-only event log as the source of truth. Powerful for audit/debug/analytics; enormous complexity in invariants, schema evolution, and projections. Use when *auditability and temporal queries are the product*, not because it's fashionable.
- ⚖️ **CQRS** — Separate command and query models. Great when read and write shapes genuinely diverge. Usually a premature doubling of code.
- ⚖️ **Microservices** — See above. *The distributed monolith is real and common; if you end up with synchronous call chains across five services, you built a distributed monolith.*
- ⚖️ **Dependency Injection Container** — A DI framework is fine; a *container* often creates a service locator with extra steps. Rust/Go/Elixir largely abandoned them for constructor wiring, and nobody misses them.
- 🔍 **Cache-Aside** — Read-through / write-through / write-behind caches. All useful; all require a *stated* invalidation strategy. There is no free lunch, there is only a stated staleness budget.
- 🔍 **Outbox Pattern** — When you need a message and a DB write to be atomic. Genuinely the right answer more often than people realize. Costs a poller and a table.
- 💬 **"A pattern is a name for a decision you already made. Make the decision first."**

### Anti-Patterns

Terms that are (mostly) insults, and knowing them saves a lot of time.

- ✅ **Big Ball of Mud** — No discernible structure, everything entangled. The core anti-pattern; the top-level diagnosis for most legacy systems.
- ✅ **God Object / God Class** — One class that knows and does everything. Usually a missing-abstraction smell: nothing else took responsibility for its responsibilities.
- ✅ **The Swiss Cheese** — Whole system, but full of holes. *(The term is generally credited to Ed Yourdon, and appears in *Revenge of the Servers* (2002); note that the identical "holes are aligned until they line up" analogy was also used by British epidemiologist Nick Lambert in 1990 to describe the failure of multiple defensive layers in aviation accidents. Lambert's is the earlier and more rigorous version — worth reading the parallel, because the software analogy gets weaker precisely where the medical one is strong.)*
- ✅ **The Pothole Effect** — Bridges that are "burned over" by repeated, well-intentioned fixes. The condition, again usually attributed to Yourdon: repeated patch-after-patch repairs produce a structure that is nominally repaired and functionally worse, because nobody can safely change it anymore. *The lesson:* if fixes keep landing on the same spot, stop patching and change the underlying process or design. A component that breaks monthly isn't unlucky; it's telling you something structural.
- ✅ **Shotgun Surgery** — One logical change requires edits in ten scattered files. *Symptom of missing cohesion.* The fix is rarely "be careful"; it's a change to a shared abstraction.
- ✅ **Cargo Cult Programming** — Copying a pattern's form without its reasoning. (Richard Gabriel, 1994 — used to be "cargo cult programmers" in The Psychology of Computer Programming.)
- ✅ **The Law of Demeter Violation — The "Train Wreck"** — One change touches everything.
- ✅ **Distributed Monolith** — See above. The systems that took the worst of both worlds.
- ✅ **Premature Abstraction** — Abstracting before you know the shape. Cheaper to fix than a bad abstraction, but the *cost of the wrong one compounds forever*.
- ✅ **Feature Creep** — The scope grows during the build.
- ✅ **Technical Debt (misused)** — See Fowler. Calling ordinary mess "debt" robs the word of meaning.
- ✅ **The Skeptic's Guide / "Works on My Machine"** — Environment-dependent behavior. Fix with hermetic builds and reproducible tooling, not discipline.
- ✅ **Mystery Meat / Legacy Spaghetti** — No names, no tests, no safe way to change anything.
- ⚖️ **Stochastic Testing / "It works 90% of the time"** — Non-determinism in the system that is supposed to be the safety net.
- ✅ **Betamancer's "programming ain't" skepticism** — Named after the *Betamancer* persona, a well-known voice arguing that most of the industry's confident claims about reuse, generality, and framework abstraction are unfalsifiable and mostly wrong. Useful as a *stance* (demand evidence for abstraction) even if you don't accept the conclusions. *(Self-aware note: this entry is deliberately hedged, because the corpus of "programming ain't" writing is mostly pseudonymous blog posts with limited primary sourcing. Take the argument, not the attribution.)*
- ✅ **Zawinski's Law of Leaky Abstractions** — "All non-trivial abstractions leak, and all abstractions are leaky because once you're in there, you can always find something that isn't abstracted." The corollary that matters: **you don't get to pick which part leaks.** So don't design a system whose correctness depends on an abstraction holding. (Joel Zawinski, 2002 — a talk whose title is usually rendered *"The Perils of Reuse"* and whose source is notoriously hard to pin down; the *idea* is sound and widely cited.)
- 💬 **"Not invented here" (NIH)** — The most durable anti-pattern in the industry, because it hides behind "we control it" while actually costing you every fix, security patch, and platform improvement upstream gives you away. *Exception, not rule:* compliance, latency, or a genuinely unique domain are legitimate reasons to own it. Write the reason down.
- 💬 **"Bikeshedding"** — Spending disproportionate argument on a trivial, low-stakes decision while the expensive decision goes unexamined. *(Origin disputed; popularized via C. Northcote Parkinson's "Why Don't We Get Jokes?" and various retellings. The metaphor: everyone has an opinion about the shed's color, silence on the foundation.)* **The fix is procedural:** explicitly rank decisions by cost-of-being-wrong, and spend your objection budget accordingly.
- 💬 **"Yak shaving"** — A chain of apparently necessary tasks, each justified by the previous one, that yields nothing you can ship. The correct response is to **shave the yak** (build the throwaway that automates the tedium, with an explicit timebox) rather than let it consume the sprint. *(Term popularized by Scott Adams, via the Stanley Dilbert comic, 1990s.)*
- 💬 **"The galloping goose"** — see above. A reminder that whatever seems finished is usually not.
- 💬 **"Cargo cult" applied to processes** — Adopting Scrum's *ceremonies* without its *purpose* (inspect-and-adapt), or Agile's *values* without its *discipline*. The ceremony without the feedback loop is theatre, and theatre is worse than nothing because it consumes the time the feedback loop needed.
- 💬 **"The best code is no code."** — Deletion is a feature. Every dependency, config flag, and abstraction you remove is a thing you never have to upgrade, document, or debug.
- 💬 **"Zero-cost abstractions"** — Rust's term, and a genuinely great standard: if the abstraction doesn't cost anything at runtime, use it freely. Measure the compile-time/runtime tradeoff honestly.

---

## Part IV — Architecture Philosophies

### Unix Philosophy

From the original Bell Labs papers, notably *Software Tools* (Kernighan & Plauger, 1976) and *The UNIX Programming Environment* (Kernighan & Pike, 1984). Read these before the blog posts about them.

- ✅ **Do One Thing and Do It Well** — The Unix root of all good design: narrow, composable, single-purpose tools.
- ✅ **"Expect the Unexpected"** — Assume the environment, inputs, and collaborators will misbehave, and be ready. *(Rob Pike.)*
- ✅ **"Be Liberal in What You Accept, Be Conservative in What You Send"** — *(Rob Pike, on program interface design.)* Accept wide variety of input, produce predictable output. Part of the philosophy of robust interfaces.
- ✅ **"Everything is a file"** — Uniformity is a feature. The same interface, applied everywhere, means the same tools work everywhere. (Plan 9 extended this to "everything is a file *descriptor*", including network connections.)
- ✅ **Mechanism over Policy** — Make the *mechanism* (the tool) general and the *policy* (the use of the tool) a thin layer on top. *(Rich Hickey: the "flip side" is also a slogan — and the harder half.)*
- ✅ **Composition of Small, Sharp Tools** — Power from assembly, not from a single super-tool.
- ✅ **Filters** — Each stage reads, transforms, writes. No shared state, no side channels, easy to test each stage, easy to reorder. (Rob Pike, "Pipes and Filters".)
- ✅ **Text as Data** — Readable, greppable, diffable, scriptable, language-agnostic. CSV, JSON, s-expressions, config files, source code itself.
- ✅ **Build Tools to Build Tools** — Never reinvent what's already good and available. *(The proverb "Don't write a Lisp in C++" is the same warning: don't implement an interpreter when you're writing an app.)*
- ✅ **Hiding Complexity** — *"The complexity of the task should be in the data, not in the code."* (Rob Pike.) The hallmark of great tools: it looks obvious in retrospect.
- ✅ **Make the Reader a Writer** — Tools whose input format is easy to produce. This is why ed, awk, and JSON won over baroque alternatives.
- 💬 **"Debugging is twice as hard as writing a program in the first place. Therefore, if you write the program as cleverly as possible, you can't debug it."** *(Kernighan & Plauger.)*
- 💬 **"When in doubt, use K&R braces."** — *Their actual term was "K&R" (Kernighan & Ritchie) and the advice was "use consistent style." Braces, indentation, naming: consistency beats correctness of style choice.*
- 💬 **"There are only two hard things in computer science: cache invalidation and naming things."** *(Phil Karlton; the first, "off by one errors.")*

### Functional & Declarative

- ✅ **Pure Functions** — Same input, same output, no side effects. Trivially testable, trivially parallelizable, trivially cacheable, trivially reasonable.
- ✅ **Immutability** — Reassignment is a bug source; produce new values. *(Haskell, Clojure, Elixir, Kotlin's `val`, Rust's non-`mut` bindings.)*
- ✅ **Referential Transparency** — An expression can be replaced by its value without changing behavior. The formalization of "no hidden state," and the compiler's strongest correctness tool.
- ⚖️ **Side Effects at the Edges** — Functional core, imperative shell. Push I/O to the boundary so the middle is testable. (Alan Kay's "The Meaning of Object-Oriented Programming", 1998.) *This is the pragmatic synthesis almost everyone lands on.*
- ⚖️ **Monads** — A composition mechanism for computations with effect. Monads are a monoid in the category of endofunctors, yes, and also a way to thread state/errors/IO through pure code. *In practice:* learn them from TypeScript/Python/Haskell basics (Option, Result, flatMap) before the category theory.
- ✅ **Declarative Over Imperative** — *What* you want, not *how* to get it. SQL, HTML, CSS, React, SQLAlchemy, Terraform, `.map`/`.filter`/`.reduce`. *So what:* the interpreter's job is to pick a good plan, not just follow your instructions.
- ✅ **Composition** — Build from small pieces that combine. The functional answer to the Unix answer — same philosophy, different substrate.
- ✅ **"Elegant is a Preference, Correctness is a Requirement."** — *Rob Pike's framing.* Optimizing for elegance means optimizing for few concepts and low surprise. Do it.
- ✅ **Total Functions / No Hidden Failure** — Make partiality explicit: `Option`, `Result`, `Result<Vec<T>>`. *(Rust's community ruleset and Clippy lints are the practical enforcement of this.)*
- 🔍 **Lazy Evaluation / Streams** — Don't compute what you don't need. Guard against leaks/strictness surprises.
- 🔍 **Algebraic Data Types** — Model a domain as `type Shape = Circle(Radius) | Rect(Width, Height)`. This is the formal root of "make illegal states unrepresentable."
- 🔍 **Pattern Matching** — Exhaustive matching on data shape, forcing the compiler to flag missing cases. The most underrated language feature for correctness.

### Object-Oriented

- ✅ **Encapsulation / Information Hiding** — Objects should hide their representation and invariants. *(Bertrand Meyer, "Object-Oriented Software Construction".)*
- ✅ **Objects Are Values With Behavior Attached** — Alan Kay's framing of OOP: not C-with-classes, but message-passing small objects with private state. The interesting reading, and the one that influenced Smalltalk and the design of the web.
- ✅ **Combine Data and Behavior** — Behavior that needs to know a data's invariants lives with the data. Otherwise invariants leak and rot.
- ⚖️ **Design by Contract** — Preconditions, postconditions, invariants checked at module boundaries. (Bertrand Meyer, Eiffel.) *Practical status:* underused, in part because run-time checks cost, and in part because you can get much of the value from types, tests, and assertions.
- ⚖️ **"Program to an Interface" (Gang of Four, preface)** — See Part I.
- 🔍 **Model–View–Controller / Model–View–Presenter** — Separate the domain from its presentation. (Trygve Reenskaug, 1979.) Arguably the most consequential MVC formulation.
- 💬 **"The best OO design is a hierarchy of plain types with behavior attached, not a class diagram."**

### Distributed Systems

The hard truths, largely from Werner Vogels' *ACM Queue* "Life Beyond Distributed Transactions" (2012) and *Designing Data-Intensive Applications* (Kleppmann, 2017). Read Kleppmann; it's the best single source in software engineering right now.

- ✅ **The Fallacies of Distributed Computing** — Classic traps: network is reliable; latency is zero; bandwidth is infinite; network is secure; topology doesn't change; there's one administrator; transport cost is zero; the network is homogeneous. *(Peter Deutsch, "The Fallacies of Distributed Computing," 1994 — he originally listed four; the canonical eight-item form is a later expansion popularized by Sun Microsystems and Jim Gray.)* You will hit every single one.
- ✅ **There Is No "Exactly Once"** — Over a network, you get at-least-once or at-most-once. "Exactly-once" is an illusion, achievable only in a narrow scope (Kafka's transactional per-partition processing) and only end-to-end with idempotent consumers. *(Kleppmann, "Please stop calling databases CP or AP"; also "It's a Lie!").*
- ✅ **Partitions Are Not Failures, They're Normal** — The network will partition. Design for it as the ordinary case, not the exception. (CAP: the choice isn't "consistency or availability" once partitions are unavoidable — it's between CP and AP *during* a partition. Design for the partition.)
- ✅ **Make Operations Idempotent** — Because retries are inevitable. Idempotency keys, dedup, UPSERT, versioned messages.
- ✅ **Idempotence and Ordering Are Separate Concerns** — At-least-once delivery + idempotent handlers + explicit ordering guarantees per key is the common, correct pattern.
- ✅ **Consistency and Latency Are a Tradeoff** — Linearizability costs you a round trip. A good system makes the tradeoff explicit, per operation, and consistent with the product.
- ✅ **Clocks Are Lies** — Never use distributed wall-clock for ordering. Use logical clocks (Lamport), version vectors, or a monotonic sequence per entity. (Kleppmann ch. 9.)
- ✅ **Concurrency Bugs Are Design Bugs** — "Heisenbugs" from shared mutable state don't get fixed by better testing; they get fixed by a design that makes the illegal interleaving unrepresentable. (C.A.R. Hoare, "Hints for Computer System Design," 1969.)
- ⚖️ **Eventual Consistency Is Fine, *Hidden* Eventual Consistency Is Not** — Async propagation is fine. Being surprised by it is not. Put the eventual bit in the design doc, in the API contract, and in the user-facing behavior. *(This is the rule, and it prevents the other 20% of the argument.)*
- ✅ **Bounded Queues and Backpressure Are Features** — Unbounded queues convert a spike into an OOM, minutes later. Choose your bound on purpose.
- ✅ **Prefer Immutable Messages / Append-Only Logs** — History you can replay, audit you didn't have to build, recovery you can test. *(Kafka, event log, append-only storage.)*
- ✅ **No Distributed Transactions** — Use saga patterns or, simpler, design so distributed atomicity isn't required. (Vogels' central claim.) "Two generals" / "atomic commitment" show the impossibility; embrace it.
- ✅ **Timeouts Must Be Set Explicitly, Everywhere** — And be pessimistic. An unset timeout is a hang; an optimistic timeout is a flapping circuit breaker.
- ✅ **Bounded Retries with Exponential Backoff and Jitter** — And a *dead-letter path* when retries are exhausted. Silent message loss is worse than a visible failure.
- ✅ **Prefer Crash-Only / Stateless Designs Where Possible** — A process that holds no state can be killed and restarted at any moment, and it's dramatically simpler. *(Vanity Fair's "You Can't Win, You Can't Lose" as crash-only software.)*
- ✅ **The Tail Is the Hard Part** — At scale, p99 dominates your users' experience and your on-call rotation. Design for the tail explicitly; it's often 100× the median's work. *(Dean & Barroso, "The Tail at Scale.")*
- 💬 **"Amateurs talk about complexity. Experts talk about the failure modes."** *(Kent Beck.)*
- 💬 **"Design for the failure you will have, not the one you expect."**

### Data-Centric Thinking

- ✅ **Data Structure + Algorithm = Program** — Niklaus Wirth, *Algorithms + Data Structures = Programs* (1976). Most of what we call "framework knowledge" is really just familiarity with standard data structures.
- ✅ **The Right Data Structure Changes the Complexity** — Choosing an index, a denormalized read model, or a trie can turn O(n) into O(1) without a line of clever code. *The best optimization is often a better data structure.*
- ✅ **Move Computation to the Data** — Databases, query engines, and vectorized runtimes are fast because they process data in bulk, close to it. So should your code. (Jim Gray.)
- ✅ **Index Everything You Query, Nothing You Don't** — The index is a data structure you pay write-time for. Make it deliberate.
- ✅ **Store Denormalized, Compute Normalized** — Read models optimized for the query; sources of truth kept canonical. The CQRS idea at its honest, minimal form.
- ✅ **Schema Evolution Is Inevitable** — Design for additive, backward-compatible change: nullable-then-backfill-then-required. Never a big-bang migration. *(Martin Fowler, "Evolutionary Database Design"; "Parallel Change".)*
- ⚖️ **Event Sourcing** — See Patterns People Misuse. *Right* when audit and temporal query are the product; *wrong* when you just wanted "a log."
- 🔍 **CQRS** — See above. Genuine when read and write shapes diverge; a doubling of code if they don't.
- 🔍 **Materialized Views / Derived State** — Precompute for reads, and own the refresh path explicitly.
- 🔍 **The Log as the Source of Truth** — Replay is the ultimate "reproducible bug report."

---

## Part V — Language & Runtime Idioms

The same principles express differently per language. This section is where the abstraction meets the metal.

### C / C++

- ✅ **K&R / C89 → C99 → C11 → C23** — Know which standard you're targeting; the idioms differ.
- 🔍 **Declarations in `for` Loops** — (C99.) The idiomatic form: `for (size_t i = 0; i < n; i++)` rather than predeclaring.
- 🔍 **`static` in a Translation Unit** — File-local linkage. Encapsulation at the module level; the C answer to "no God object."
- 🔍 **`const` Everywhere** — Tells the reader (and the compiler) "this doesn't change." `const char *` vs. `char * const` vs. `const char * const` is worth memorizing.
- 🔍 **Single-Definition Rule (C) / ODR (C++)** — One definition per symbol, program-wide. Violations are ODR violations (C++) or flat-out undefined behavior (C).
- 🔍 **Rule of Zero / Rule of Five / Rule of Three** — If you define a destructor, copy, move, or `operator new/delete`, understand which of the three you need. *(C++ Core Guidelines: "R.0: Define special member functions only when you need to.") Prefer Rule of Zero: use `std::vector`/`std::string` and write none of them.*
- 🔍 **`std::string_view` / `string_span`** — Non-owning, cheap, zero-copy string parameters. (C++17.)
- 🔍 **RAII** — Resource Acquisition Is Initialization. Every resource wrapped in a type whose destructor cleans it up. *This is why C++ has no `finally` and needs none, and why `with`/`using` in other languages is the same idea.*
- 🔍 **Move Semantics** — Transfer ownership cheaply, leave the source in a valid-but-unspecified state. (C++11.)
- 🔍 **Prefer Composition to Inheritance** — Public inheritance is a promise you can't take back. *(Effective Java Item 18; C++ Core Guidelines C.35.)*
- 🔍 **Exception Safety Levels** — Basic, strong, and nothrow guarantees. If you're unsure which you're providing, you're providing the first. *(C++ Core Guidelines E.6.)*
- ⚖️ **Exceptions vs. noexcept vs. Errors** — Throw for exceptional conditions; use `noexcept` in destructors and moves; return status codes in hot paths if you must. (John Lakos, "Large-Scale C++".)
- 🔍 **The Rule of Least Surprise for `delete`** — Match `new` with `delete`/`delete[]` correctly; the classic C/C++ bug. (C++17's `std::unique_ptr` largely fixes this.)
- 💬 **"In C++, there are two kinds of code: the code that has RAII and safe objects, and the code that doesn't."**

### Go

Kent C. Condie's *Effective Go*, the Code Review Comments, and Rob Pike's talks. The language's idioms are unusually consistent because the standard library and reviewers enforce them.

- ✅ **"Accept interfaces, return structs"** — *Go Proverbs.* Interfaces belong to the *caller*, not the implementer. Define the interface where it's consumed.
- ✅ **"The bigger the interface, the weaker the abstraction"** — Rob Pike. One or two methods is a good interface. *"Make the method count."*
- ✅ **Clear is Better than Clever** — *Go Proverbs.* If it needs a comment to explain, make it simpler.
- ✅ **Don't Repeat Yourself, but "A little copying is better than a little dependency"** — Copy a small function rather than build an abstraction that couples two things that will diverge.
- ✅ **Getters Should Be Omitted** — Accessors add noise, hide invariants, and invite scattering. Export fields or provide methods when behavior is actually needed. (Effective Go.)
- ✅ **Mixed Caps for Multiword Names, Not Underscores** — `MaxLength`, not `Max_Length`.
- ✅ **`io.Reader`/`io.Writer`/`io.ReaderFrom`** — "Small interfaces make code composable" made concrete. Go's standard library is the proof: tiny one-method interfaces compose into an enormous ecosystem of consumers (bufio, gzip, crypto, HTTP) without any of them knowing about the others. *The design rule:* an interface with one method is nearly always right; `io.Four` is a warning.
- ✅ **Errors Are Values** — *Go Proverbs.* Errors are ordinary values: compare with `errors.Is`/`errors.As`, wrap with `%w`, handle them early. *This is a design decision, not a limitation — it means errors flow as data.*
- ✅ **Panic for Truly Exceptional, Error for Everything Else** — *Go Proverbs.* Panics are for programming errors and unrecoverable states, not flow control.
- ✅ **`defer` Immediately After the Error Check** — Record the intent next to the acquisition. *Also why `defer` is the basis of Go's resource management (no RAII needed).*
- ✅ **Zero Value Useful** — Types should be usable as declared. This is a real design constraint: it means no "constructor required for a valid value."
- 💬 **"If it's worth doing, it's worth testing."** — Rob Pike. (Frequently misquoted as "worth doing *badly*," which inverts the meaning entirely. The original Gopherfest talk is a call to add tests, not to lower the bar.)
- 🔍 **Zero-Value-Nice API & Constructors** — `sync.Mutex{}` and `sync.Once{}` are usable without `New`. Ask: does your type satisfy this?
- 🔍 **Errors Wrapped with `%w`** — The structured way to carry context and inspect the chain. (Go 1.13.)
- 🔍 **Functional Options for Constructors** — `func NewServer(addr string, opts ...Option) *Server` instead of a config struct with 15 nullable fields. This is the Go answer to the builder pattern.
- 🔍 **Channels as Queues, `context.Context` for Cancellation** — `ctx` is the standard way to pass deadlines and cancellation; it flows with the call graph. `select` with `ctx.Done()`.
- ⚖️ **Channels vs. Mutexes** — Channels for communication and ownership transfer; `sync.Mutex` for protecting a struct's internal state. *Rule of thumb:* "don't communicate by sharing memory; share memory by communicating" (the Go proverb) is aspirational, not a law.
- 🔍 **`go` + `sync.WaitGroup` for Concurrency** — `errgroup` for concurrency with error propagation. *Sane defaults:* Goroutines are cheap, but goroutine leaks are still real bugs; every spawned goroutine needs a guaranteed exit.
- 🔍 **Table-Driven Tests** — The canonical Go testing idiom. `for _, tt := range tests { t.Run(tt.name, ...) }`. Names your cases; the subtests pay off forever.
- 🔍 **Variadic Options / Embedding for Composition** — Anonymous struct embedding for method reuse; *remember* it also promotes fields, which is sometimes surprising.
- ⚖️ **`interface{}` → `any` → generics** — `any` is an alias for `interface{}` (Go 1.18) for readability. Generics added real value for slices/maps/containers without erasure; a good example of a language growing carefully.
- 💬 **"Clear is better than clever."** — *Go Proverbs.*
- 💬 **"Errors are values."** — *Go Proverbs.*
- 💬 **"Don't communicate by sharing memory; share memory by communicating."** — *Go Proverbs.*

### Rust

- ✅ **Ownership, Borrowing, Lifetimes** — The type system enforces at compile time that data is uniquely owned or shared immutably, never mutated while shared. *This is a whole class of data races and use-after-free removed by construction.*
- ✅ **The Type System Is Your Enemy's Enemy** — It's fine to be "fighting the borrow checker." It's also how you get guarantees that would take a runtime lock in every other language.
- ✅ **Make Illegal States Unrepresentable** — Rust's flagship application. `Option<T>`, `Result<T, E>`, newtypes, typestate.
- ✅ **No `unsafe` Without a Documented Invariant** — *Rust API Guidelines, F.1–F.2.* Unsafe is a contract with your future self, written down.
- ✅ **`?` Operator for Error Propagation** — Propagate and convert in one character. Idiomatic Rust returns `Result` from almost every fallible function, and most functions are fallible.
- ✅ **`unwrap()`/`expect()` Only When Failure Is a Programming Bug** — Not for "should never happen" at runtime with external input. Clippy's `unwrap_used` lint and the `expect` message convention are the standard.
- ✅ **`panic!` for Unrecoverable, `Result` for Recoverable** — Same spirit as Go, different spelling.
- ✅ **Newtype for Type Safety** — `struct UserId(u64)` won't pass where `OrderId` is expected. A zero-cost type-state machine.
- 🔍 **Trait as the Interface** — Traits are the interface, but the *generic* form (`fn f<T: MyTrait>`) is preferred for static dispatch; `dyn Trait` only when you need heterogeneity. *(The "trait objects vs. generics" rule of thumb: generics for performance, `dyn` for type erasure.)*
- 🔍 **`impl Trait` and RPITIT** — Return types you don't want to name. Getting much better ergonomics.
- 🔍 **Iterators and Zero-Cost Abstractions** — The core of Rust's design philosophy: expressive code, compiled away. *Admit honestly:* iterator chains can hurt compile times and debuggability; the Rust Book now explicitly teaches `for` loops first.
- 🔍 **`cargo` Ecosystem** — `cargo clippy` (the linter that teaches idioms), `cargo fmt`, `cargo test`, and the ubiquitous `Result` convention. Clippy is arguably the best code-teaching tool in any language.
- ⚖️ **Async Rust** — Powerful and still stabilizing (poll-based, `Send` bounds, executor choices). *Honest status:* the borrow checker meets `async` in genuinely hard places; the ecosystem is converging but the ergonomics are not fully settled.
- ⚖️ **Macro Hygiene** — Macros can express a lot; they're also where Rust readability problems live. *Pragmatic:* prefer functions and generics, reach for macros for DSLs and truly repetitive code.
- 💬 **"Make illegal states unrepresentable."** — the Rust community's motto, and it's the most portable design lesson here.
- 💬 **"No unsafe without a soundness argument."**

### Python

- ✅ **Explicit is Better than Implicit** — *Zen of Python, item 2* (PEP 20). Type hints, keyword-only args, no cleverness.
- ✅ **Readability Counts** — *Zen, item 7.* Code is read far more often than written. Optimize for the reader.
- ✅ **There Should Be One Obvious Way to Do It** — *Zen, item 12.* Not always achievable, but when you break it (e.g., five different idioms for the same list operation), you've made a mess.
- ✅ **The Zen of Python, item by item** — 19 aphorisms, arguably the best single-page design philosophy in any language. Print it.
- ✅ **Type Hints for Boundaries** — Especially at module edges and in public APIs. Python 3.5+; `typing` matured a lot, and `mypy`/`pyright` in CI pay off.
- ⚖️ **The GIL and Its Retirement** — CPython's Global Interpreter Lock serializes bytecode execution within one process, so plain threads don't give CPU parallelism. The removal is a **three-phase, multi-year project**, and the phase numbers matter:
  - **Phase I (Python 3.13, Oct 2024)** — free-threaded build available but explicitly **experimental** (PEP 703).
  - **Phase II (Python 3.14, Oct 2025)** — free-threaded build is **officially supported**, no longer experimental, but **still opt-in, not the default** (PEP 779, accepted June 2025).
  - **Phase III (undecided)** — free-threading as the default. Explicitly deferred to a future PEP.
  - Tradeoffs to know before you commit: roughly **5–10% single-thread overhead**, meaningful multi-thread speedup on CPU-bound work, and **~15–20% higher memory use**.
  - *The lesson, independent of CPython's roadmap:* performance characteristics are architecture decisions, not language trivia. And the ecosystem strategy is "let C do the loops" — NumPy and friends release the GIL and are fast anyway.
- 🔍 **Duck Typing** — "If it walks like a duck..." — implicit interfaces via behavior, enabled by PEP 484 / `typing.Protocol`. **`Protocol` is the modern, explicit form of duck typing** and a very good idea.
- 🔍 **Context Managers (`with`)** — Deterministic cleanup, no exceptions, works for anything with `__enter__`/`__exit__`. `contextlib.contextmanager` for custom ones.
- 🔍 **Decorators** — Functions that wrap functions. Order of operations is `@app.route` below `@app.route`. Useful and easy to overuse; a nested closure is often clearer.
- 🔍 **Comprehensions** — Idiomatic data transformation: list/set/dict comprehensions, and the generator expression for lazy evaluation. (Readability note: very long comprehensions are usually a sign to extract a function.)
- 🔍 **The GIL-Releasing Ecosystem** — NumPy, and the wider "let C do the loops" pattern. *Design lesson:* know where your code's actual time goes.
- 🔍 **`__slots__`** — Memory-efficient instances by banning `__dict__`. The idiomatic answer to Python's memory overhead for many small objects.
- ⚖️ **Namedtuples vs. dataclasses vs. attrs vs. Pydantic** — `dataclass` is the default for structured data (stdlib, clean, typed). Namedtuples when you want tuple behavior/tuples for compatibility. `attrs`/`Pydantic` for validation and more. *Don't over-engineer a small record.*
- ⚖️ **Inheritance vs. Composition in Python** — Python's `duck typing` makes composition usually simpler; avoid deep multiple-inheritance hierarchies.
- 💬 **"Simple is better than complex."** — *Zen, item 3.*
- 💬 **"Beautiful is better than ugly."** — *Zen, item 1.* When a design is clean, it *tells you* the right way to use it. (Tim Peters.)

### JavaScript & TypeScript

- ✅ **The Two-Problem Family of JavaScript** — (Doug Crockford.) ASI and type coercion. The solution, historically, was "be careful and use a linter." The modern solution: TypeScript.
- ✅ **Strong Typing Is Non-Negotiable Now** — *In practice:* TypeScript's cost is low (gradual adoption, JSX-compatible) and its value is high (documented interfaces, safer refactors, editor autocomplete). The idiom is **strict mode + no `any` in public APIs + a lint rule banning it.**
- ✅ **`unknown` Over `any`** — `unknown` forces you to narrow; `any` opts out. *Rule of thumb:* if you need `any`, you haven't figured out the type yet.
- ✅ **Discriminated Unions for State** — Model mutually exclusive states as a union with a literal discriminant field. TypeScript's narrowing then makes illegal states unrepresentable. *This is a genuinely great design idea and the most valuable TS idiom.*
- ✅ **Narrowing, `satisfies`, and Type Guards** — Teach the compiler what it can't infer. `satisfies` (4.9+) lets you validate a shape *while keeping its precise type*, which is a real ergonomic win over `as`.
- 🔍 **Immutability for React & State** — `const` + spread; immutability libraries for deep structures. *Why:* reference equality drives change detection and cheap memoization.
- 🔍 **Functional Core, Imperative Shell** — Keep React components about rendering; put effects/logic in plain functions and hooks. Test the pure parts.
- 🔍 **Promise Discipline** — `async`/`await`; handle rejection; avoid promise pyramids; `Promise.all` for parallelism; never mix `await` in a loop by accident (sequential, not parallel). Unhandled rejections are the #1 runtime bug.
- ⚖️ **ES Modules vs. CommonJS** — ESM is the standard; respect the interop pain during transition.
- ⚖️ **Loose Equality (`==`) vs. Strict (`===`)** — Crockford's rule: always `===`. Automatic type coercion in `==` is a rich source of `0 == "0"` bugs. *(Also `null` vs. `undefined` vs. missing: know which one your API returns.)*
- ⚖️ **`undefined` vs. `null`** — `undefined` = absent/unset; `null` = explicitly empty. Be consistent; ESLint rules help. Mixing them is a reliable source of confusion.
- ⚖️ **The Prototype Chain and `this` Binding** — `this` is determined lexically by how a function is called, not where it's defined. `call`/`apply`/`bind` fix it. *The honest advice:* use arrow functions for callbacks, regular functions for methods, and `.bind` in constructors.*
- 🔍 **Event Loop and Microtasks** — `setTimeout` (macrotask) vs. `Promise.then` (microtask) ordering; the microtask queue drains before the next macrotask. This explains most "why did that log out of order" bugs.
- 🔍 **`package.json` & `node_modules` SemVer** — Caret (`^`) vs. tilde (`~`) ranges; lockfiles are the source of truth for reproducibility. "It works locally" is a dependency-resolution problem.
- 💬 **"There are only two hard things in computer science: cache invalidation, naming things, and off-by-one errors."**
- 💬 **"Simplicity is the ultimate sophistication."** — Design principle, not a JS-specific truth.

### Java & JVM

- ✅ **Composition Over Inheritance** — *(Effective Java, Item 18.)* Java's single class inheritance plus deep hierarchies of implementations of interfaces is a trap. Prefer composition.
- ✅ **"Favor Composition Over Inheritance" and "Program to Interfaces, Not Implementations"** — *(Effective Java, Items 18 and 64.)* Still the two highest-value items.
- ✅ **`final` Classes and `Objects.requireNonNull`** — Make your invariants enforced by the compiler and constructor. (Joshua Bloch's advice to make classes final by default.)
- ⚖️ **Checked Exceptions** — The classic debate. Bloch says they're a mistake for the most part (differently declared = duplication, lambda-hostile); others defend them for recoverable conditions. *Pragmatic:* Java APIs you write today lean toward unchecked, documented exceptions.
- ⚖️ **Null** — Tony Hoare's "billion-dollar mistake" (2009) admits null was his idea. (Sir Tony's Null Reference — International Conference on Communication Technology, 1986.) *Modern mitigation:* `Optional` (poorly understood, not a field type), nullability annotations (JSpecify — still evolving), `@NonNull` tooling, or Kotlin.
- ✅ **Generics and Type Erasure** — Java's type safety is compile-time only; the runtime sees raw objects, which is why `new T[]` needs an unchecked cast and why unchecked operations are a *warning* rather than a compile error. ⚠️ **Correction to a common claim: C# shares Java's erasure model** — it is *not* reified (only value types get partial runtime type identity, and `typeof` behaves differently). **Kotlin and Scala are the JVM languages that reify generics.** *Design lesson:* don't build APIs around runtime type introspection; a type parameter is a compile-time convenience, not a runtime capability.
- ⚖️ **Records and Sealed Classes** — Java 16/17 brought algebraic-data-type-flavored modeling: `record` for data, `sealed` + pattern matching (Java 21) for exhaustive state. This is the "make illegal states unrepresentable" lesson arriving in Java.
- 🔍 **The JVM's Real Advantage: GC, JIT, and Portability** — Write once, run anywhere, plus extremely strong runtime optimization. The cost: startup time, memory footprint, and a ceiling on some deployment models (though GraalVM native image is closing that).
- 🔍 **Streams** — Declarative data processing, and a real "declarative over imperative" idiom. *Beware:* `parallelStream` is a footgun, and streams can be less readable than a plain loop for simple cases. (Joshua Bloch: "Don't use streams for side effects.")
- 🔍 **Immutability** — Effectively final, `List.copyOf`, `record`, `String`. Java's mainstream best practice; the safest code to share across threads.
- 💬 **"When in doubt, favor composition over inheritance."** *(Effective Java.)*

### SQL & Databases

- ✅ **The Database Is Not Your application's Storage Layer — It *is* your application** — *The first rule of a serious system: the model is the product.* Modern ORMs mean the schema shapes your product's capabilities.
- ✅ **You Shapes, It Forms** — The best architectures start with the data model, not the endpoints. (Richardson.) Get the schema right; the rest is easier than you think.
- ✅ **Prefer Explicit Over Implicit** — Explicit `JOIN`s, explicit `UNION` (vs. `OR`), explicit transaction boundaries. *Rule of thumb:* SQL's implicit behaviors (type coercion, NULL three-valued logic, implicit casts) are where the "weird bug" lives.
- ✅ **Learn NULL's Three-Valued Logic** — `NULL = NULL` is `NULL`, not `TRUE`. `NOT IN` with a NULL in the subquery returns no rows. This single fact explains most surprising SQL.
- ✅ **Index What You Query** — A B-tree index, a covering index, a partial index, a composite index in the right column order. *Rule:* a composite index `(a, b)` serves `a` and `(a,b)` but *not* `b`.
- ⚖️ **Normalization (1NF–3NF/BCNF)** — Normalize until it hurts, denormalize until it works. Joins are cheap; wrong data isn't.
- ✅ **The Two-Phase Commit Is Usually the Wrong Answer** — Distributed transactions in the application database are avoided for good reason; prefer local transactions + an **outbox** pattern or eventual consistency.
- ✅ **Batch Your Writes** — One `INSERT` with 10,000 rows beats 10,000 round trips. Batching is the single biggest database performance lever.
- ⚖️ **ORM vs. Query Builder vs. Raw SQL** — Raw/composable SQL (e.g. `sqlc`, `kysely`, `drizzle`) keeps SQL as SQL while getting type safety. Often the modern sweet spot. *Tradeoff:* raw SQL requires real SQL skill; that's not a downside.
- ✅ **ACID vs. BASE, and CAP, Correctly** — CAP is about *behavior during a network partition*, not a general consistency/availability choice; partition tolerance isn't optional. *Know the actual guarantees your database offers:* isolation levels (Postgres `READ COMMITTED` is not `SERIALIZABLE`) and where it falls short. (Kleppmann, "Please Stop Calling Databases CP or AP.")
- 🔍 **Materialized Views** — Precomputed read models, refreshed on schedule or on change.
- 🔍 **Key-Value, Document, Graph, Time-Series, Vector** — Each store's access pattern dictates its fit. *Match the store to the access pattern and the consistency requirement, not to the hype.*
- 🔍 **Connection Pools** — Every serverless function exhausting its pool is a classic outage. Know your pool limits and where they are.
- 💬 **"It works in my database"** — the SQL edition.
- 💬 **"Premature optimization is the root of all evil" is about *micro*-optimizations; the real wins are almost always a schema or index fix.** (Donald Knuth's original phrasing was about 95% of premature optimizations being misguided — the pithy quote is a simplification.)

### Shell & Automation

- ✅ **Fail Fast, `set -euo pipefail`** — The first line of a script should be `set -euo pipefail`. Unset variables (`-u`) catch the classic "expands to nothing" bug. *But understand it:* `-e` has sharp edges in loops and pipelines; use explicit error handling where it matters.
- ✅ **Quote Everything, Always** — `rm "$file"`, never `rm $file`. Unquoted variables in scripts are the #1 source of both bugs and, occasionally, `rm -rf /` incidents.
- ✅ **Handle Filenames with Spaces and Globs** — Always `"$@"` when forwarding arguments, never `$*`. If you must handle arbitrary input, you need care.
- ✅ **Compose, Don't Reimplement** — Pipe to `jq`, `sed`, `sort`, `awk`, `xargs -0` rather than reimplementing in a language you're less careful in.
- ✅ **Idempotent, Declarative Automation** — Idempotent scripts can be re-run safely. *Use:* declarative tools (Terraform, Ansible, Nix, package manifests) over long imperative scripts where you can; they'll be readable and re-runnable.
- 🔍 **Shebangs & Shell Strictness** — `#!/usr/bin/env bash` for portability; `#!/bin/sh` and POSIX-only constructs for maximum portability.
- 🔍 **Heredocs & `xargs -0`** — Null-delimited for filenames with weird characters; heredocs for readable inline blocks.
- 🔍 **`trap` for Cleanup** — Guarantee cleanup on exit, including failure.
- 💬 **"Shell is a programming language, not a scripting language."** — Treat scripts as code: lint them (ShellCheck), review them, quote them, test them.

---

## Part VI — Performance & Scale

- ✅ **Measure First, Optimize Second** — Without a measurement, you're guessing, and guesses are usually wrong. Profiling tools exist; use them. (Brian Kernighan: "Unix systems used to be slow, and then people started using profilers to find the bottlenecks.")
- ✅ **Amdahl's Law** — Optimizing a 5% component buys you 5%. Find out where the time actually goes first. Optimize the common path; the rare path is where latency *feels* bad but costs nothing in aggregate.
- ✅ **Amdahl's cousin: The Common-Case Trap** — The *common* case deserves the most care: the cold path is where bugs hide, but it's also where a fraction of a percent of your throughput lives. Optimize the 99th-percentile path and call it done.
- ⚖️ **Bret Taylor's Three Questions for Performance** — (1) How much faster can it be? (2) What's the cost in complexity? (3) How often is the cost paid vs. how often is the speed gained? *The best performance work is usually the option that wins on all three.*
- ✅ **Locality of Reference (Cache)** — Modern CPUs and memory are hierarchical; locality dominates. *Why:* data movement costs orders of magnitude more than arithmetic — a main-memory access is roughly two orders slower than an L1 cache hit, so the "cheap" operation (a load) is often the expensive one. Optimize the layout and the data structure, not the loop. (Hennessy & Patterson, *Computer Organization and Design*; their CACM series on hardware consistency makes the memory-wall argument more sharply than most software books do.)
- ✅ **Amortized Analysis** — Many operations cost O(1) amortized even if individual ones are O(n) (dynamic array append, hash table insert). *Why it matters:* it lets you design simple structures with predictable aggregate cost.
- ✅ **Caching Is the Last Resort, and a Debt** — Every cache is a consistency liability: invalidation, staleness, thundering herd, and cold-start problems. *Use it* for genuinely expensive-to-recompute data; measure the hit rate or delete it. *(See the "only two hard things" joke.)*
- ✅ **Batching Amortizes Round Trips** — Latency, not bandwidth, is usually the cost. Combining 1000 requests into 1 changes the game far more than making one request faster.
- ✅ **The Tail Latency Dominates the User's Experience** — Dean & Barroso, "The Tail at Scale." A p99 of 2 seconds makes a p50 of 50ms feel slow. Hedge requests, avoid queueing, and measure p99 not mean.
- ⚖️ **Sharding vs. Partitioning vs. Replication** — *Sharding* splits data for capacity; *partitioning* organizes it; *replication* copies for availability. Choose a partition key that keeps hot data together — the single biggest scaling decision you'll make, and the hardest to undo. *(A bad partition key is the most expensive mistake in distributed systems.)*
- ⚖️ **Synchronous vs. Asynchronous** — Sync is simple and correct; async is fast and complex. *Rule:* make it sync by default; go async where you can prove you need the latency back.
- ✅ **Add Caches Only With a Measured Hit Rate** — A cache with a 5% hit rate is pure overhead (plus invalidation bugs). Instrument before and after.

---

## Part VII — Security Mindset

This is where software engineering becomes responsible engineering. None of the below is optional, and none is a "nice-to-have."

- ✅ **Never Trust Input** — Validate and sanitize at the boundary. Parameterized queries over string concatenation. Whitelist over blocklist. (OWASP Top 10.)
- ✅ **Never Roll Your Own Cryptography** — Use audited, standard libraries (NaCl/libsodium, `bcrypt`/`Argon2` for passwords, TLS libraries for transport). Inventing crypto is famously, reliably broken.
- ✅ **Least Privilege for Every Identity** — The service account, the CI token, the IAM role, the DB user. Least privilege is not just a principle, it's a containment strategy. (See "Principle of Least Privilege.")
- ✅ **Defense in Depth** — Assume each layer will eventually fail; don't make one check load-bearing.
- ✅ **Fail Securely** — On any error or timeout, deny by default. (The opposite of fail-open, which turns a bug into a breach.)
- ✅ **Keep Dependencies Minimal — and Patched** — Every dependency is code you didn't write, running with your privileges. SBOMs, automated updates, and knowing your transitive tree are part of the job now.
- ✅ **Separate Code from Data (Injection Defense)** — No string concatenation into SQL, shell, LDAP, HTML, or a template engine. Parameterize everywhere. (OWASP #1.)
- ✅ **Secrets Never in Code or Config in the Repo** — Use a secret manager, environment injection, or vault. Rotate, scope, and audit. Commit history is forever; assume any secret that touched a repo is compromised.
- ✅ **Authenticate the User, Then Authorize the Action** — Authentication (who) is not authorization (may they). BOLA/IDOR — checking that the *request* is valid but not that this *user owns this object* — is the most common real-world web vulnerability. (OWASP API #1.)
- ⚖️ **Security Is Not a Gate at the End** — Retrofitting security breeds security debt and siloed reviews. Bake it into design (threat modeling, secure defaults) and into CI (SAST, dependency scanning) so it's continuous, not a phase.
- ⚖️ **Encrypt vs. Focus on Impact** — Encryption-in-transit and at-rest are hygiene, not a plan. Prioritize by what an attacker actually wants: your auth system, your secrets, your supply chain, your business logic. *A 2019 Capital One breach was an SSRF in a misconfigured WAF, not a cryptography failure.*
- 🔍 **Threat Modeling** — "What can go wrong?" before you build, not after the incident. (STRIDE, Adam Shostack.) Cheap, and catches design-level problems no scanner will.
- 🔍 **2FA / Passkeys / Hardware Keys** — Phishing-resistant authentication for anything privileged. *Design principle:* prefer the more secure default and let users opt *down*, not up.
- 🔍 **Zero Trust** — Never trust, always verify — per request, not per network location. Assume the network is already compromised.
- 🔍 **Security Headers and Safe Defaults** — CSP, Secure/HttpOnly/SameSite cookies, `frame-ancestors`, proper TLS config. The best practice is the default.
- 🔍 **Security Logging and Incident Response** — Log auth and authorization events; assume you'll need them. (You have an incident-response plan *and* you've rehearsed it.)
- 💬 **"Amateurs hack systems, professionals hack people."** — Bruce Schneier's formulation of the actual threat model.

---

## Part VIII — Team & Process Mentalities

### Agile Flavors

- ✅ **The Manifesto Is a Set of Values, Not a Process** — Individuals and interactions over processes and tools; working software over comprehensive documentation; collaboration over contract negotiation; responding to change over following a plan. *Read the "right" column too* — it says the left matters *more*, not that the right is worthless. (Wadler et al., 2001.)
- ✅ **The Second Half of the Manifesto Matters** — "While there is value in the items on the right, we value the items on the left more." Most process disputes ignore this.
- ⚖️ **Scrum vs. Kanban vs. XP** — Scrum is a container with sprints and ceremonies; Kanban is flow and WIP limits; XP is engineering discipline (pairing, TDD, refactoring, CI). *They compose better than they compete.* A great delivery process is *flow* (Kanban) + *discipline* (XP) + a *cadence* (Scrum's) + explicit *definition of done*.
- ✅ **The Definition of Done Is the Point** — An explicit, shared checklist: reviewed, tested, documented, monitored, deployed, accepted by users. *Without it, "done" means whatever the person who said it meant, and nobody can trust it.*
- ✅ **Sustainable Pace Is a Feature** — Cramming is a loan against the next sprint. Teams do not get faster by working less consistently; they get *done* more predictably. (Sustainable pace, iterative increment — Scrum Guide.)
- ✅ **Retrospectives** — A cadence for improving how the team works, not just the work. Pointless only if the same issue keeps recurring and nothing changes.
- ✅ **XP Practices Still Hold Up** — Pair programming, test-driven development, refactoring, continuous integration, simple design, collective code ownership, whole-team availability. (Kent Beck, 1999.) This is a great starting read for engineering practice independent of framework.
- ⚖️ **Estimating (Story Points, Velocity)** — Useful for *conversation* about relative size, less useful as a *promise*. Story points measure relative, team-specific effort. Treating them as hours reliably misleads. (Never convert points to dates.)
- ✅ **The Team Should Own the Whole Thing** — "You build it, you run it" (Werner Vogels). Teams that only ship don't learn what they built; on-call without shipping builds bad instincts. Not a slogan — a staffing and rotation decision.
- 🔍 **Kanban WIP Limits** — The most underrated practice: limiting work-in-progress surfaces the actual bottleneck faster than any retrospective.
- 🔍 **Continuous Integration and Continuous Delivery** — Integrate at least daily; a merge queue is a strong pattern. Deploy when ready, not in batches — small deployments are less risky, not more.

### Technical Debt

Fowler's framing, read it directly: [Is High Quality Software Worth the Cost?](https://martinfowler.com/articles/is-quality-worth-cost.html)

- ✅ **Technical Debt Is a Deliberate Shortcut** — With a known reason (usually: ship now, fix later) and, crucially, with the interest rate you signed up for. Unprincipled mess is not debt.
- ✅ **Debt Has an Interest Rate** — Some shortcuts are near-free (`// TODO` with a clear fix); others compound (a bad data model, a missing abstraction, a wrong package boundary). *Pay the high-interest debt first.*
- ✅ **"Good code is cheap; bad code is expensive"** — Cost isn't just writing; it's every future change, every review, every onboarding, every incident caused by it. Refactoring is an investment, not a distraction.
- ✅ **The Debt Portfolio View** — Not all debt is equal. Some is deliberate, documented, and tracked (good debt). Some is just neglect. Track it like a portfolio; prioritize by interest rate, not by what's loudest.
- ✅ **Bounded Refactoring Time** — "Golden afternoon" refactoring; a culture of small, regular investment beats a heroic (and never-arriving) rewrite.
- ⚖️ **The Big Rewrite** — Rarely succeeds. The codebase you're rewriting is the one where the requirements are least known. The second-system effect (Brooks) applies. *Better:* Strangler Fig — grow the new system alongside the old, migrating piece by piece. (Martin Fowler.)

### Estimation & Deadlines

- ✅ **People Are Bad at Estimating, and That's Fine** — The study of judgment under uncertainty (Kahneman, Tversky). Estimates are guesses with a confidence interval. Present them that way.
- ✅ **Give Ranges, Not Points** — "Two to three weeks" beats "March 15." (And the range is usually *wider* than feels honest; that's the calibration lesson.)
- ✅ **Timeboxing Over Deadlines** — "We'll spend one week on this, then reassess" is honest. A fixed end date on uncertain work is a promise you'll break. (This is the argument for spikes: *timebox learning*, not build the thing.)
- ✅ **Ship in Small, Regular Slices** — Smaller batches mean shorter feedback loops, less risk, and earlier correction. A big-bang release is a bet on your ability to be right for months.
- ✅ **A Deadline Is a Budget, Not a Wish** — A real deadline forces tradeoffs: scope, quality, or resources. Name which one you're cutting. (Donella Meadows, *The Systems Thinker*.) Vague "do it by Friday" hides the tradeoff until it's too late to plan.
- ✅ **Estimation Should Include the Work Nobody Plans** — Code review, integration, testing, deployment, monitoring, documentation, and the operational follow-up. Most overruns are here, not in "writing the code."
- ⚖️ **The Two-Week Sprint Promise** — Fixed-cadence sprints with commitments are common but add pressure. Sprints as *feedback cycles* (not *promises*) work better. (Schwaber & Sutherland's later writings on empiricism echo this.)
- 🔍 **Spikes** — Timebox to answer a specific question, then throw away the code. Cheap way to buy certainty.
- 🔍 **Proofs of Concept vs. Spikes** — PoCs are "does it work at all," spikes are "how would we do it." Both are throwaway. Neither should silently become production code without a rewrite.

### On-call, Support, and Ownership

- ✅ **You Build It, You Run It** — Shared responsibility for operations, not a separate "ops" team downstream. (Vogels.) Teams that ship but never operate make different mistakes, and the two feedback loops are the point.
- ✅ **Blameless Postmortems** — When a system fails, the human is usually working with the information the system gave them (or didn't). Blame stops reporting; blameless postmortems find the real cause. (John Allspaw, 2012; SRE Book ch. 1.)
- ✅ **Error Budgets** — SLOs give you a budget; when you're within it, ship; when you're over it, fix reliability. This turns "reliability vs. features" from an argument into arithmetic. (Google SRE.)
- ✅ **Alert on Symptoms, Not Causes** — Alert on what your users care about (latency, error rate), not on internal causes (CPU high). CPU isn't a problem until latency is. (Google SRE Workbook ch. 5.)
- ✅ **Every Alert Should Be Actionable and Owned** — If an alert needs no action, delete it. Alert fatigue trains people to ignore alerts, which is how you miss the real one.
- ✅ **Runbooks for Known Failures** — The best fix for a 3am page is a step-by-step document written calmly in advance. Automate the runbook later.
- ✅ **Practice the Failure** — Game days, chaos experiments, fire drills. The point isn't to find bugs, it's to find the runbook gaps and the missing permissions.
- ✅ **Toil Is a Bug** — Anything manual, repetitive, and automatable shouldn't be a human's permanent job. Google SRE's "eliminating toil" is the clearest framing. (The Site Reliability Book ch. 5.)
- ✅ **On-call Should Be Sustainable** — Rotation limits, handoffs, and comp are real engineering constraints. A team burning out is a reliability risk, not just a wellbeing one.
- 🔍 **Observability > Monitoring** — Logs, metrics, traces, and profiles. The three pillars. (Charity Majors' "Observability is Superior to Monitoring" is the sharpest modern argument.) *Design question:* can you debug this system without reproducing the bug?
- 🔍 **SLOs, SLIs, and Error Budgets** — Define the Service Level *Indicator*, set an *Objective*, spend the *Budget*. A great mental model for reliability conversations. (Google SRE ch. 4.)
- 💬 **"Hope is not a strategy."** — The whole point of an on-call rotation.
- 💬 **"It's not done until it runs in production and someone's watching."**

### Product & User Empathy

- ✅ **Users Don't Want Features; They Want Problems Solved** — Start with the job to be done, not the feature list. (Clayton Christensen, *The Innovator's Dilemma*; Teresa Torres' "The Mom Test" for the interviewing discipline.)
- ✅ **Solve the Problem, Not the Requested Solution** — Users are experts in their problem, not in your solution space. The first request for "a button that does X" is rarely the best answer; asking why reveals the actual job.
- ✅ **The Best Interviewer Asks About the Past, Not the Future** — People are terrible at predicting their future behavior and great at recalling their last time. (The Mom Test.)
- ✅ **Ship, Measure, Iterate** — Not everything is knowable before you build it. Reduce the cost of being wrong (spikes, prototypes, A/B tests, MVPs) rather than trying to eliminate being wrong.
- ✅ **The Documentation Is Part of the Product** — An unauditable, unexplainable system gets abandoned, no matter how good the code is.
- ⚖️ **Disagree and Commit** — (Intel's Andy Grove.) Once a decision is made, execute it wholeheartedly — and keep the door open for new information. *Alternative,* from Amazon: "disagree vigorously, then commit fully." The difference is whether you argued *well*, not whether you argued.
- ✅ **Nobody Is Neutral** — Design, naming, defaults, and error messages shape user behavior. Assume your choices have effects; measure them. (Dan Lockhart, *The Choice*. Nudge theory, Thaler & Sunstein.)
- ✅ **Defaults Are Decisions** — The default option is the option most people get. Choose it deliberately. (A "no" default opt-in for data sharing is stronger than an opt-out.)
- 🔍 **The Cost of Switching** — The best retention strategy is a product users are unhappy to leave. (Bessemer, a good lens on developer tools.)
- 💬 **"Do things that don't scale."** — Paul Graham, on manual, high-touch service while you find the product. Applies to internal tools and developer experience too.
- 💬 **"The best way to predict the future is to invent it."** — Alan Kay.

---

## Part IX — Software as a Craft

### Wisdom, Proverbs, Aphorisms

- ✅ **Simplicity is Prerequisite for Reliability** — Edsger W. Dijkstra. *The thesis:* the complexity you don't remove is complexity you can never fully verify. Reliability is a function of what you can reason about.
- ✅ **"Simplicity is the ultimate sophistication"** — Leonardo da Vinci. *Carry it far enough and it's a design principle: the most sophisticated solution is often the one that reduces concepts, not adds them.*
- ✅ **"The best way to predict the future is to invent it"** — Alan Kay. *On agency through tooling.*
- ✅ **"Beware of premature generalization"** — Fred Brooks, *The Mythical Man-Month* (1975). *Why it applies:* generalization is a bet on future requirements; do it once, with evidence.
- ✅ **"The Mythical Man-Month"** — Brooks's central lesson: adding people to a late project makes it later (Brooks's Law), because communication paths grow faster than work. Communication overhead is the hidden cost. (*Note: the second edition, "No Silver Bullet" (1986), is the better half — the essential vs. accidental complexity distinction is more useful than the first edition's scheduling content.)
- ✅ **"No Silver Bullet" (Essential vs. Accidental Complexity)** — Brooks, 1986. *Why it matters:* some complexity is essential to the problem (inherent, hard to remove); some is accidental (tooling, process, environment). *It tells you where to invest.* The second essay, "No Wolves," on schedule games, is the practical corollary.
- ✅ **"The Galloping Goose" / "Galloping geese, beware"** — W. Richard Stevens, *UNIX Programming for the Advanced* (1992). *The lesson:* OSes get slow in small, unexpected ways. **Profile, don't guess.**
- ✅ **"Premature Optimization Is the Root of All Evil"** — Donald Knuth, 1971 (structured data). *The real, precise version:* premature optimization (before profiling) is bad; late optimization (after you know it's needed) is good. Design for the general case, then measure, then optimize the hot path. 95% of premature optimizations misguidedly ignore Knuth's own caveat; the honest version is the one above.
- ✅ **"Beware of computer scientists building models of the world"** — Peter Naur, *Programming as Theory Building* (1985). *Why it matters:* the theory the programmer builds is the real system; the code is a residue. It's why onboarding is hard and why the original designer leaving is such a risk. **Programming is theory building; write the theory down.**
- ✅ **"Programming as Theory Building"** — Same source. The deepest epistemology in the craft: understanding is not in the program text.
- ✅ **"It is easier to change the specification to fit the program than vice versa"** — a cautionary (David Wheeler). *The productive version:* in an agile setting, treat the spec as a living contract and *say* when it changes, rather than silently diverging.
- ✅ **"The best way to design a system is to write the code"** — Occasionally wrong, but almost always *more* right than designing longer in the abstract. Make the theory by typing it.
- ✅ **"The most damaging phrase in the language is 'We've always done it this way'"** — *Attributed to Grace Hopper; treat as attributed-but-unsourced.* *The positive form, which is the useful part:* a convention is a hypothesis, not a law. Revisit it when the context that justified it has changed — and note that "we've always done it this way" is often a true statement being used as a non sequitur.
- ✅ **"First, solve the problem. Then, write the code"** — Johnson & Johnson, quoted in the First Round CAPTCHAs review (1966). The idea *predates* Agile. Old ideas, newly fashionable.
- ✅ **"Do not add requirements that aren't needed now"** — a form of the YAGNI principle, and the note in Brooks on "how much generality to build."
- ✅ **"Nothing can be said to be a universal best practice"** — the recurring punchline of **P59**, the paper "The Fallacy of the Eternal Big Programmer" (Paul Woodside, Derek Rothberg, David Farley, 2019). *Right: context rules.* A practice is good *for a goal in a context.* Ask what goal, in what context, before you adopt anything. Even this list.
- ✅ **Reuse Is an Outcome, Not a Goal** — The most defensible anti-reuse argument is economic, not aesthetic. Building a reusable component is *several times* harder than building a single-use one, and you cannot validate generality against an unknown future. Reuse that shows up in a healthy codebase is almost always a **side effect** of writing concise, well-scoped code for a concrete problem — not a result of designing for reuse up front. (Robert Glass, *Facts and Fallacies of Software Engineering*; Jeff Atwood, "The Delusion of Reuse and the Rule of Three," 2004; Uwe Friedrichsen, "The Reusability Fallacy," 2020. Friedrichsen's sharpest point: software's production cost is *already* near-zero, so the physical-world "assemble from parts" economics don't transfer.)
- ✅ **"Software is a gas"** — Brian Kernighan. *(Attributed to Bill Daley at Bell Labs, popularized by Kernighan, and now primarily a slogan rather than a sourced claim — included for the idea, not the citation.)* *The lesson:* it expands to fill whatever space you give it — which is exactly why *boundaries, ownership, and explicit constraints* are the job.
- ✅ **"Design is not just what it looks like and feels like. Design is how it works."** — Steve Jobs, paraphrasing Dieter Rams' 10 Principles of Good Design. Rams' tenth principle: *"Good design is as little design as possible."* Less, but better.
- ✅ **"I have not failed. I've just found 10,000 ways that won't work."** — Thomas Edison, on invention. *The application to software:* most code you write should be cheap to throw away. Prototypes, spikes, and experiments exist so that the 9,999 aren't part of production.
- ✅ **"Make it work, make it right, make it fast"** — Kent Beck, on TDD's red-green-reflect cycle, extended from Hoare. *The order matters:* correctness before speed. "Make it fast" last, because premature speed often ruins the first two.
- ✅ **"Refactoring is a form of engineering hygiene"** — Kent Beck.
- ✅ **"Design Patterns: Elements of Reusable Object-Oriented Software"** (the GoF book, 1994) — "A pattern describes a problem which occurs over and over again in our environment." *The point is a vocabulary, not a template.*
- ✅ **"The Zen of Python"** — Tim Peters, 19 aphorisms. Arguably the highest signal-per-line philosophy in the language ecosystem.

### Anti-Wisdom

Rules that sound wise and are actually how projects fail. **Knowing these is as valuable as knowing the principles** — they are the failure modes of over-applying the good ideas above.

- ⚖️ **"Real programmers don't use comments"** — An overcorrection. *The right version:* the code should be self-explanatory; comments explain the *why* the code can't show. Blanket avoidance of comments isn't purity, it's losing information.
- ⚖️ **"Never write comments"** — Same problem. The absence of a "why" comment is information loss disguised as virtue.
- ⚖️ **"No premature optimization" used to justify no design** — Not the same as not optimizing early. Designing clean, cohesive boundaries *is* early design, and it's cheap. Premature optimization is about *micro-optimizations before measurement*, not about skipping design.
- ⚖️ **"Move fast and break things"** — Meta's slogan, widely regretted (a real outage in 2012 was caused by a bad release). The correction: *"move fast, and be responsible."* Speed without safety is just a debt with a countdown. (Now Meta's own mantra.)
- ⚖️ **"10x engineers"** — A productivity-myth that survived from a well-intentioned blog post. It measures the wrong thing and encourages heroics over systems. The right question is "how do we make the whole team faster?"
- ⚖️ **"Velocity is a measure of productivity"** — Output metrics (story points, lines of code, commits) reliably incentivize the wrong things (gaming, churn, tiny commits). The right measures are outcomes: defect rate, time-to-value, user impact.
- ⚖️ **"Code coverage of 80% is a quality target"** — Coverage is a floor, not a goal. Teams that chase 80% write shallow, assertion-free tests. Measure what could hurt, not what the tool counts.
- ⚖️ **"More microservices = more scalable"** — Often the opposite until you have the teams, the tooling, and the operational maturity. Microservices trade code organization problems for distributed-systems problems.
- ⚖️ **"Technical debt is a failure of discipline"** — Sometimes true, often not. Often debt is a rational response to a real deadline with no slack. Understand the rate and the reason before judging.
- ⚖️ **"There's no place for 'it works on my machine' in a professional team"** — as an excuse. As a *symptom*, it's a real signal: your environment is non-hermetic, your dependencies are unpinned, or your setup is undocumented. Fix the environment, not the person.
- ⚖️ **"Agile means no planning"** — Agile is the opposite: it's *better* planning, continuously, based on real feedback instead of a stale forecast. "Agile" as an excuse to avoid thinking has nothing to do with the Manifesto.
- ⚖️ **"Document everything"** — Documentation that rots is worse than none: it misleads. Document the *important, durable* things (the *why*), and link rather than duplicate.

### Learning & Growth

- ✅ **Read the Code** — The best way to learn a system is to read its source and history. `git log` on a file tells you the story; the code alone doesn't.
- ✅ **The Documentation Is a Starting Point, Not a Source of Truth** — Trust the code and the tests; docs lag. (Docs rot, code compiles.)
- ✅ **"It's Not a Bug, It's a Feature"** — Sometimes true. But used as a *defense*, it usually means the *specification* was never agreed. Ask which it is. (Adam Badow, Git.)
- ✅ **Rubber-Duck Debugging** — Explain the problem out loud, line by line, to an inanimate object. *Origin story:* Brian Kemp's "Why the Hell Did I Use a Duck?" (1999), in which a colleague suggested a rubber duck because *she* had no programmer to talk to; the technique was then popularized by Ward Cunningham in his blikiwiki and spread through the agile community. The line from that story worth remembering: **"If you explain to the duck why your program doesn't work, you'll see the error before you can even run it."** The act of explaining forces you to externalize assumptions you were reading past.
- ✅ **"The best debugger is a clear mind"** — but pair it with real tools: `git bisect`, a profiler, a test. Reasoning without measurement is guessing; measurement without reasoning is noise. You want both.
- ✅ **Learn from Incidents, Not Just Features** — Build; break it; learn; fix. The most valuable engineering happens after the failure, if you're paying attention.
- ✅ **Fight the Right Fight** — Which problems are *yours* to solve, and which are noise? (Lindy? No.) Prioritize the ones that matter to users and the business; not every edge case is worth your time.
- ✅ **Keep a Decision Log** — Not just what you did, but what you *rejected* and why. This is what makes the next engineer's job possible. (The most underrated artifact in most organizations.)
- ✅ **The Best Time to Refactor Was Before; the Second Best Is Now; the Third Best Is Never (because it's a rewrite)"** — the general shape of the Boy Scout rule applied to timing.
- ⚖️ **"Read the Docs"** — good advice; bad advice as a substitute for reading the code. Do both; the code is the contract.
- 💬 **"The good news is, we don't need to improve anything."** — Kent Beck, sarcastically, about test coverage as a goal.
- 💬 **"It's not a test-driven-development problem if there's no tests."** — Kent Beck, on a different topic, but a useful framing of when process advice doesn't apply.

### Ethics

- ✅ **Software Has Consequences** — This is engineering, not neutral plumbing. Decisions about defaults, data collection, algorithmic recommendations, and access have real effects on real people.
- ✅ **Privacy Is Not a Feature, It's a Constraint** — Collect the minimum, store it the shortest, encrypt it, be able to explain it. This is the basis of modern privacy regulation (GDPR, CCPA) and of good practice regardless of jurisdiction.
- ✅ **Do No Harm — To Users** — Don't ship dark patterns: deceptive interfaces, confirmshaming, fake urgency, mislabeled buttons, and forced subscriptions. (Brignull / Mathur's taxonomy of dark patterns.) It's a design defect, not a cleverness feature.
- ✅ **Accessibility Is an Engineering Requirement, Not a Feature** — WCAG, semantic HTML, keyboard navigability, screen-reader support, contrast. *Why it's ethics and not just quality:* it determines who can use your product at all. A web accessibility lawsuit is not a hypothetical.
- ✅ **Report Vulnerabilities Responsibly** — When you find a security issue, follow a responsible-disclosure process (contact the vendor, give them time, publish with credit after a fix) rather than going public immediately. (Many programs now run bug bounties for this.)
- ✅ **Don't Build Surveillance You Don't Want to Exist** — The "would you be comfortable if your feature were used against you or your family?" test. It's a real heuristic for whether a design is one you'd defend.
- ✅ **Be Honest About What Your Software Does** — No dark patterns, no fake urgency, no misleading defaults. Technical honesty is a core engineering value. And never, ever deceive about security ("this is end-to-end encrypted" when it isn't).
- ✅ **Leave the Code Better Than You Found It — Including for the Next Person** — Not just the code: the comments, the tests, the docs. Your successor is a real person, often a stranger, often you.
- ✅ **Know When to Say No** — The most ethical engineering skill is declining to build the harmful thing well. It's a hard skill, and it's real work.
- 🔍 **Algorithmic Fairness & Bias** — Training data encodes past bias; models reproduce it at scale. Auditing for disparate impact is part of responsible deployment. (Cathy O'Neil, *Weapons of Math Destruction*.)
- 🔍 **Security Researchers vs. Users** — Responding responsibly to discovered vulnerabilities, running a bug bounty, and having a disclosure policy are signs of a mature organization.
- 💬 **"You are morally responsible for what you put in the world."** — The argument for engineering ethics (Van Peters, "The Most Important Feature"). *The companion thought:* most software is invisible; most damage is invisible; the people affected rarely see the code. That's precisely why it's your responsibility.

---

## Part X — The Canon: Primary Sources

The best advice lives in a small number of primary sources. If you read these, you'll have covered most of the wisdom in this list with more nuance than any list can provide.

**Design & Architecture**
- *Design Patterns: Elements of Reusable Object-Oriented Software* — Erich Gamma, Richard Helm, Ralph Johnson, John Vlissides (1994). The GoF book. Start here.
- *A Pattern Language* — Christopher Alexander, Sara Ishikawa, Murray Silverstein (1977). The origin of the word "pattern" in software. Fascinating and different.
- *Domain-Driven Design* — Eric Evans (2003). Ubiquitous Language, bounded contexts, strategic design. Transformative if you're designing a system with real domain complexity.
- *Patterns of Enterprise Application Architecture* — Martin Fowler (2002). The pragmatic companion to DDD.
- *Designing Data-Intensive Applications* — Martin Kleppmann (2017). The single best book on systems design. Fallible only in being generous to the systems it covers.
- *Building Microservices* — Sam Newman (2nd ed., 2021). The balanced, un-hyped treatment.
- *Software Architecture: The Hard Parts* — Ford, Parsons, Komic (2020). Honest about distributed-systems tradeoffs.
- *The Timeless Way of Building* — Christopher Alexander. The philosophical root of pattern thinking.
- *The Psychology of Computer Programming* — Gerald Weinberg (1985). The human, still-relevant, uncomfortable half of engineering.

**Language & Craft**
- *Code Complete* — Steve McConnell (1991/2004). The definitive on code-level craft.
- *The Pragmatic Programmer* — Andy Hunt & Dave Thomas (1999). 20th anniversary edition (2019). The pragmatism in the title is a philosophy, not a buzzword.
- *Clean Code* — Robert C. Martin (2008). Genuinely good; also genuinely abused, often by people who missed the Boy Scout / "you won't always agree" framing. Read with judgment.
- *The Clean Coder* — Robert C. Martin. The professional-responsibility argument.
- *Effective Java* (3rd ed.) — Joshua Bloch. And *Effective Go*, *Rust API Guidelines*, *Eloquent JavaScript* for their own ecosystems.
- *Structure and Interpretation of Computer Programs* — Abelson & Sussman. The SICP of *ideas*; best for thinking about computation, not for a job.
- *Programming Pearls / Programming in the Large* — Jon Bentley / John Brooks. Column and essay collections with the occasional gem.

**Distributed Systems & Reliability**
- *The Site Reliability Book* — Beyer, Jones, Petoff, Murphy (2016). Free online. The best practical resource for running systems.
- *Designing Data-Intensive Applications* (above) — the theory; this is the operations.
- *Database Reliability* — Goldman Sachs, 2020 (free online). A rare, honest account of what production databases really demand.
- *Life Beyond Distributed Transactions* — Werner Vogels, *ACM Queue* (2012). Free. The clearest short statement of why the industry abandoned two-phase commit.
- *Kafka: The Definitive Guide* — Confluent. The practical guide to one archetypal streaming system.
- *The Tail at Scale* — Dean & Barroso, CACM (2013). Free. The paper that made latency a first-class concern.

**Testing & Quality**
- *Unit Testing: Principles, Practices, and Patterns* — Vladislav Antonov / Steve Mesmans (2024). The modern, practical replacement for "xUnit Test Patterns."
- *xUnit Test Patterns* — Meszaros (2007). The taxonomy of test smells.
- *Working Effectively with Legacy Code* — Michael Feathers (2004). The seams technique and the practical art of getting under test.
- *The Art of Unit Testing* — Roy Osherov.
- *Google Testing Blog / Software Engineering at Google* — Chapter on testing; the "Test Sizes" framing is a great mental model.

**Thinking & Wisdom**
- *The Mythical Man-Month* & *No Silver Bullet* — Frederick Brooks. The essential/accidental complexity distinction and the 95% quote are both Brooks.
- *Peopleware* — Tom DeMarco & Timothy Lister. On why schedule pressure destroys teams, decades of evidence.
- *The Soul of a New Machine* — Tracy Kidder. On craftsmanship and flow.
- *The Coming of Post-Industrial Capitalism / In the Age of the Smart Machine* — Daniel Bell. Sometimes cited, over-interpreted; still a useful lens on knowledge work.
- *The Foundations of C++ / Thinking Like a Computer Scientist* — Allen Downey, freely available. Because the fundamentals matter and they're free.
- *Gödel, Escher, Bach* — Douglas Hofstadter. The book that gives you the *feel* for what computation, mind, and meaning are. Skim or read cover to cover; either way it changes how you think.

**Management & Team**
- *The Mythical Man-Month* (above) — on adding people to a late project.
- *Crucial Conversations* — Stone, Heen, Patton. On the conversations that actually stall engineering work.
- *The Goal* — Goldratt. The origin of the Theory of Constraints; why work on the bottleneck.
- *Turn the Ship Around! / Scaling Teams* — Lars Rendón, the Spotify model; a well-documented case study in caution.
- *Accelerate* — Forsgren, Humble, Kim. The empirical research on what actually makes teams fast — and it's not most of what people guess.
- *The Staff Engineer's Path* — Will Larson, and the *Staff Engineer* anthology (2023). The best current thinking on senior technical role, and on why you don't have to manage people to have impact.
- *A Philosophy of Software Design* — John Ousterhout (2nd ed., 2021). The most underrated design book of the 2010s. Deep modules, strategic vs. tactical programming, "design it twice," define errors out of existence. Free chapter 2 excerpt and errata at `web.stanford.edu/~ouster/aposd.php`.

---

## Verifying Quotes and Claims

This list is opinionated, but it should not be *sloppy*. Two categories of sourcing problem show up in software-idiom lists, and both are worth naming:

**1. Quotes that are widely misattributed.** These are common enough that repeating them is a small credibility tax:

| Commonly repeated | Actual status |
|---|---|
| "If it's worth doing, it's worth doing badly" | Rob Pike said *"if it's worth doing, it's worth **testing**."* The "badly" version inverts the meaning. |
| "Premature optimization is the root of all evil" | Real (Knuth, 1974), but his full point includes that **premature generalization is worse** and that most premature optimizations are misguided. The truncated version is used to argue for sloppiness. |
| "Simplicity is the ultimate sophistication" | Leonardo da Vinci — but no primary text has been produced. Treat it as a modern aphorism wearing a Renaissance costume. |
| "Not everything should be made reusable" | Widely attributed to a pseudonymous author; **the argument is sound, the citation is not.** The defensible sources are Glass, Atwood, and Friedrichsen. |
| "Einstein: if you can't explain it simply, you don't understand it" | No evidence Einstein said this. He *did* say he'd fail a student who used jargon to show understanding rather than simply describing it — which is usually what the quote is trying to say. |

**2. Quotes whose *source* is hard to pin down.** These are often real ideas with unwieldy provenance — Zawinski's leaky abstractions, "software is a gas," "bikeshedding." Rather than fabricate a citation, this list marks them as attributed-but-unverified. **If you can find a primary source, replace the hedge with the source.** That is the single highest-value contribution anyone can make to this document.

**3. Facts that go stale.** Anything with a version number, a date, or a "current" status will rot. The Python/GIL entry above already carries explicit phases and dates so it can be *checked* rather than *trusted*. Prefer entries that state the tradeoff over entries that state a rule — rules rot, tradeoffs don't.

**A note on the markers.** ✅ and ⚖️ are claims about *consensus*, not about *truth*. A ✅ entry is one where deviating requires a stated reason, not one that is guaranteed correct. And where an entry conflicts with another, the correct response is P59's question, not a search for the winner: [the fallacy of the eternal big programmer](https://research.swtch.com/tocs/rough_zoom/2019-01-31-xay/lpd-bug.pdf) (Woodside, Rothberg, Farley, 2019).

---

## Contributing

This list lives by being argued over. Contributions welcome, but with a bar:

**A good addition** has (at least):
- a **clear one-line definition** that a competent engineer would agree with;
- a **"so what?"** — the reason it matters, not just what it is;
- **honest tradeoffs** or an explicit marker that it's contested (⚖️), and a note about when it *doesn't* apply;
- ideally a **primary source**, and a real-world example (good or bad);
- enough **signal** that a working engineer would find it useful in a design review.

**Please don't add**:
- framework- or library-specific "best practices" that go stale in six months (🔍 exists for genuinely durable technique, not for a tool's current API);
- advice with no mechanism ("write better code," "care about quality") — if you can't say *why* it works, it's a slogan;
- something already covered — check the ❌ already-idiomatic sections first.

### Quick template

```
- ⚖️ **[Name]** — [one-line definition]. *So what:* [why it matters].
  *Counter-argument / doesn't apply when:* [honesty].
  (Source: [Author, *Work*, Year].)
```

### The One Rule Above All

Every entry here is a **starting point, not a truth**. Before adopting any of them, answer P59's question: **this is a universal best practice, for whom, in what context, and toward what goal?** If you can't answer that, you don't understand the practice well enough to apply it — which is itself the most useful lesson this whole list is trying to teach.
