# Session 1: Why Type Theory? (28/10/26)

## Main Resources

* Russell's Paradox, Stanford Encyclopedia of Philosophy: https://plato.stanford.edu/entries/russell-paradox/
* Thorsten Altenkirch, *Should Type Theory Replace Set Theory as the Foundation of Mathematics?*: https://arxiv.org/abs/2111.06368
* The HoTT Book, the Introduction only: https://www.cs.uoregon.edu/research/summerschool/summer14/rwh_notes/hott-book.pdf

## Some History

Around the turn of the 20th century, Frege was trying to build all of arithmetic on top of pure logic. One of his basic principles was what we now call *naive comprehension*: for any property $\phi(x)$ you can write down, there is a set

$$\{ x \mid \phi(x) \}$$

of exactly the things satisfying it. This seems completely reasonable, and it's how most of us think about sets day to day.

In 1902 Russell wrote to Frege pointing out that it doesn't work. Take $\phi(x)$ to be $x \notin x$ and let

$$R = \{ x \mid x \notin x \}.$$

Now ask whether $R \in R$. If it is, then by definition $R \notin R$. If it isn't, then it satisfies the defining property, so $R \in R$. Either way we get a contradiction. Frege's system was inconsistent, and the second volume of his *Grundgesetze* went to print with an appendix admitting as much. This more or less stopped his project.

There were two broad reactions:

* **Fix set theory.** Zermelo (and later Fraenkel) restricted comprehension so that you can only carve a subset out of a set you already have: $\{ x \in A \mid \phi(x) \}$. This became ZFC, which is what most mathematicians nominally work in today.
* **Change the language.** Russell's own fix was a *theory of types*, developed with Whitehead in *Principia Mathematica*. Every object lives at some level (individuals, sets of individuals, sets of sets of individuals, ...) and a set can only contain things from the level below it.

The second route kept going:

* **Russell** introduced types (1900s) to get rid of the paradoxes.
* **Church** combined types with his $\lambda$-calculus (the *simply typed* $\lambda$-calculus, 1940), giving a theory of functions where every term has a type.
* **Per Martin-Löf** (1970s onwards) developed *intuitionistic* or *dependent* type theory, where types can depend on terms. This is the ancestor of what Lean, Agda and Rocq run on.
* **Voevodsky** and others (2000s–2010s) developed *homotopy type theory* (HoTT), which reads types as spaces and adds the *univalence axiom*. This is the most modern form of the subject and the one the HoTT book covers.

## How Type Theory Gets Around Russell

1. **Every term comes with a type, and you can't ask about membership.** In set theory, "$x \in y$" is a proposition you can ask about any two things. In type theory, "$a : A$" is not a proposition. It's a *judgement*: part of how the expression was built, which you (or the computer) check by looking at the text. You can't form the proposition "$a : A$" and then negate it, so there's no "$x \notin x$" to begin with.

   As an example, in set theory you can happily define a function on sets by cases:
   $$f(x) = \begin{cases} 0 & \text{if } x \in \mathbb{N} \\ 1 & \text{otherwise.} \end{cases}$$
   In type theory there's nothing like this. A function $f : A \to B$ already says what its inputs are, and there's no way to take an input and ask which type it belongs to. If $x : \mathbb{N}$ then that's simply how $x$ was given to us. "$x : \mathbb{Z}$" isn't false, it just isn't a statement we can write down. To treat $x$ as an integer we have to convert it explicitly (see *coercions* below).

2. **Comprehension is always relative to a type.** The closest you can get to "the collection of $x$ with property $\phi$" is a predicate $P : A \to \text{Prop}$ on some type $A$ you already have. So Russell's set would have to be something like $R : A \to \text{Prop}$. But then to "ask whether $R \in R$" you'd need to write $R(R)$, which needs $R : A$. It isn't. $R$ has type $A \to \text{Prop}$, so the expression doesn't typecheck. The paradox just isn't a well-formed sentence.

3. **Types of types are stratified.** We do want a "type of all types", called a *universe*. If we allowed $\text{Type} : \text{Type}$, a version of the paradox comes back (Girard's paradox). Instead there's a hierarchy
   $$\text{Type}_0 : \text{Type}_1 : \text{Type}_2 : \cdots$$
   which is really Russell's original idea of levels in a modern form. (Lean users will recognise this as `Type`, `Type 1`, `Type 2`, ...)

So ZFC avoids the paradox by restricting *which* sets exist, while type theory avoids it by restricting *what you can say*.

## Basic Differences

* **Typing is static.** As above, $a : A$ is a property of the expression and its context, not a fact about the universe you can question. In set theory, $2 \in 3$ is a meaningful (and, with the usual encoding, true) statement. In type theory, the analogous question isn't one you can ask.

  If you've programmed before, this is the difference between a dynamically typed language like Python (where you can check `isinstance(x, int)` while the program runs) and a statically typed one, where the compiler checks every type before anything runs. Type theory is firmly the second kind. In Lean:

  ```lean
  #check (2 + 2 = 5 : Prop)   -- fine: a perfectly good (false) proposition
  #check ("hello" + 2 = 5)    -- error: rejected before we even get to ask if it's true
  ```

  Note the difference: $2 + 2 = 5$ is a well-formed statement that happens to be false. $\texttt{"hello"} + 2 = 5$ isn't a statement at all. Ill-typed expressions are not false, they're meaningless, and Russell's $R \in R$ ends up in this second category.
* **Equality behaves differently.** Voevodsky's *univalence axiom* says, roughly, that isomorphic structures are equal. More precisely, for types $A$ and $B$, equality $A = B$ is equivalent to having an equivalence $A \simeq B$. This is how mathematicians already talk ("the group of order 2"), and univalence makes it literally true.
* **Universal properties pin things down exactly.** In set theory, an object defined by a universal property (a product, a quotient, a free group, ...) is only unique up to isomorphism, and you have to keep carrying the isomorphisms around. In HoTT, with univalence, "unique up to unique isomorphism" becomes plain uniqueness: the solution is unique as an element of the relevant type.

## Notation, Functions, and Propositions as Types

The key idea is *propositions as types* (the Curry–Howard correspondence). A proposition is a type, and a proof of it is a term of that type. Once you accept that, logic becomes a special case of building types, and the connectives turn into ordinary type formers.

| Logic | Type theory | A proof is... |
| --- | --- | --- |
| $p \implies q$ | $p \to q$ | a function turning proofs of $p$ into proofs of $q$ |
| $p \land q$ | $p \times q$ | a pair of proofs |
| $p \lor q$ | $p + q$ | a proof of one side, tagged with which side |
| $\text{False}$ | $\mathbf{0}$ (the empty type) | impossible: there are no terms |
| $\text{True}$ | $\mathbf{1}$ (the unit type) | the single trivial term |
| $\neg p$ | $p \to \mathbf{0}$ | a function from proofs of $p$ into the empty type |
| $\forall x : A,\ P(x)$ | $\prod_{x : A} P(x)$ | a dependent function |
| $\exists x : A,\ P(x)$ | $\sum_{x : A} P(x)$ | a pair (witness, proof) |

A couple of these deserve more than a table row.

**Negation.** $\neg p$ is just $p \to \mathbf{0}$. To prove $\neg p$, you show that any proof of $p$ could be turned into an element of the empty type, which can't exist. This is exactly "assume $p$ and derive a contradiction", but now it's a function.

### $\Pi$-types (dependent functions)

An ordinary function $f : A \to B$ sends each $x : A$ to something in a fixed type $B$. A *dependent* function lets the output type vary with the input: given a family of types $B(x)$ indexed by $x : A$, a term

$$f : \prod_{x : A} B(x)$$

sends each $x : A$ to some $f(x) : B(x)$.

This is precisely what a proof of a universally quantified statement is. A proof of $\forall n : \mathbb{N},\ n + 0 = n$ is a function that takes any $n$ and hands back a proof of $n + 0 = n$. The *type* of that proof depends on $n$, so we need a $\Pi$-type. Ordinary functions $A \to B$ are the special case where $B$ doesn't depend on $x$.

### $\Sigma$-types (dependent pairs)

Dually, a term of

$$\sum_{x : A} B(x)$$

is a pair $(a, b)$ where $a : A$ and $b : B(a)$. Again, the type of the second component depends on the first. When $B$ is constant this is just the product $A \times B$.

Read logically, a term of $\sum_{x : A} P(x)$ is a witness $a$ together with a proof that $P(a)$ holds, which is a constructive proof of $\exists x : A,\ P(x)$.

The same type also plays the role of the set-builder $\{ x : A \mid P(x) \}$: its elements are elements of $A$ bundled with evidence that they satisfy $P$. So "the even numbers" becomes $\sum_{n : \mathbb{N}} \text{isEven}(n)$.

(A small caveat for later: a $\Sigma$-type remembers *which* witness you gave, which is more information than "there exists" usually carries. HoTT and Lean deal with this using propositional truncation. We'll come back to it.)

For more on $\Pi$- and $\Sigma$-types, with plenty of examples you can run, see the *Dependent Type Theory* and *Quantifiers and Equality* chapters of *Theorem Proving in Lean 4*: https://lean-lang.org/theorem_proving_in_lean4/

## The Law of Excluded Middle

In classical logic we take for granted

$$\forall p : \text{Prop},\ p \lor \neg p.$$

Under propositions as types, a proof of this would be a function that, given *any* proposition, either produces a proof of it or a refutation of it. There's no way to construct such a thing in general, so in type theory LEM is not provable. It's not refuted either: you can add it as an axiom (Lean does, via `Classical.em`), and lots of mathematics happily does.

To make sense of this we need to say what a "proposition" is:

* **Propositions** are types with *at most one element*. Any two proofs of a proposition are equal. We only care *whether* a proof exists, not which one we have.
* **Propositional extensionality**: if two propositions are logically equivalent ($p \to q$ and $q \to p$), then they are equal. This is a special case of univalence.

The distinction matters. LEM for propositions is consistent with HoTT. LEM for *all* types (every type is either inhabited or empty, with a choice of inhabitant) contradicts univalence. So the restriction to propositions isn't pedantry.

## Representation

### Natural numbers

In set theory, everything is a set, so the natural numbers have to be *encoded* as sets. There are several ways to do it:

* **von Neumann:** $0 = \emptyset$, $n + 1 = n \cup \{n\}$, so $3 = \{0, 1, 2\}$.
* **Zermelo:** $0 = \emptyset$, $n + 1 = \{n\}$, so $3 = \{\{\{\emptyset\}\}\}$.

Both work, but they give different answers to meaningless questions like "is $1 \in 3$?" (yes for von Neumann, no for Zermelo). The mathematics we care about never depends on the choice, but the foundations let you ask.

In type theory, $\mathbb{N}$ is defined *inductively*: it's the type freely generated by

* $0 : \mathbb{N}$
* $\text{succ} : \mathbb{N} \to \mathbb{N}$

and that's it. There's no encoding and nothing to "look inside". Induction and recursion come for free as the way you use this type. (If you've played the Natural Number Game, this is exactly the definition it starts from.)

### Subsets

"Subset" doesn't really make sense in type theory, because a term has exactly one type. There's no sense in which $\mathbb{N}$ literally sits inside $\mathbb{Z}$. Instead:

* To use a natural number as an integer, we use a **coercion**: a function $\mathbb{N} \to \mathbb{Z}$ that the system inserts for us. Lean does this all the time, and it's why you sometimes see little $\uparrow$ arrows in goals.
* To talk about a "subset" of a type $A$, we use a **predicate** $P : A \to \text{Prop}$. Think of $P(x)$ as "$x$ is in the subset".

Given two such predicates $P, Q : A \to \text{Prop}$, inclusion is defined by

$$P \subseteq Q \quad :\equiv \quad \forall x : A,\ P(x) \to Q(x).$$

If we want the subset as a type in its own right, we take $\sum_{x : A} P(x)$ from before.

## Why Proof Assistants?

You'll notice Lean keeps coming up. That's not a coincidence: most modern proof assistants (Lean, Agda, Rocq) are built directly on dependent type theory, and the reason is propositions as types.

If a proof is just a term, and a theorem is just its type, then *checking a proof* is the same thing as *type checking a term*. Type checking is a purely mechanical process, exactly the kind of thing computers are good at. So the computer doesn't need to understand your argument. It only needs to check that the term you've built really has the type you claimed. The part that does this (the *kernel*) is small enough that people can trust it.

This is also part of why Voevodsky got involved. He'd found errors in his own published work and wanted a foundation where proofs could be checked by machine. The two areas have grown up together ever since. Big results like the Four Colour Theorem (in Rocq) and the Liquid Tensor Experiment (in Lean) have been fully formalised, and Lean's mathlib now covers a large chunk of undergraduate maths and beyond.

If you want to try it, the Natural Number Game linked in the README is the easiest place to start.

## Questions to Think About

* Why is $R \in R$ not a valid statement in type theory? Try to explain it in a sentence.
* What does a proof of $\forall n : \mathbb{N},\ n + 0 = n$ look like as a function? What goes in, and what comes out?
* In set theory, is $1 \in 3$? Why does type theory not let us ask this?
* Which do you find more natural, ZFC or type theory? Why?
