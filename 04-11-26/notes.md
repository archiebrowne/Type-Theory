# Session 2: The Curry–Howard Isomorphism (04/11/26)

## Main Resources

* Peter Selinger, *Lecture Notes on the Lambda Calculus*, Sections 6.1–6.5: https://arxiv.org/pdf/0804.3434
* Philip Wadler, *Propositions as Types* (optional, for the history): https://homepages.inf.ed.ac.uk/wadler/papers/propositions-as-types/propositions-as-types.pdf

Last week we talked about how "propositions are types and proofs are terms". This week we discuss the **simply-typed $\lambda$-calculus** and **propositional intuitionistic logic** and show the this correspondence. The plan is:

1. Set up the simply-typed $\lambda$-calculus and its typing rules.
2. Say what intuitionistic logic is and why it's the right logic here.
3. Set up natural deduction for intuitionistic logic.
4. Prove that the two systems are really the same thing.

## 1. The Simply-Typed $\lambda$-Calculus

### Types

We start with some set of *basic types*, written $\iota$ (think "integers" or "booleans"). The simple types are then built up by

$$A, B ::= \iota \ \mid\ A \to B \ \mid\ A \times B \ \mid\ 1$$

* $A \to B$ is the type of functions from $A$ to $B$.
* $A \times B$ is the type of pairs.
* $1$ is the type with exactly one element (the "unit" type).

Conventions: $\times$ binds tighter than $\to$, and $\to$ associates to the right, so $A \to B \to C$ means $A \to (B \to C)$.

### Terms

The *raw terms* are

$$M, N ::= x \ \mid\ M\,N \ \mid\ \lambda x^A.M \ \mid\ \langle M, N\rangle \ \mid\ \pi_1 M \ \mid\ \pi_2 M \ \mid\ *$$

That is: variables, application, $\lambda$-abstraction (now with the type of the variable written on it), pairs, the two projections, and the single element $*$ of $1$.

"Raw" because nothing stops us writing nonsense like $\pi_1(\lambda x.M)$ (projecting out of a function) or $\langle M, N\rangle\,P$ (applying a pair to something). The typing rules are what rule these out.

### Typing judgements

A **typing judgement** looks like

$$x_1 : A_1,\ \dots,\ x_n : A_n \ \vdash\ M : A$$

and reads: "assuming each $x_i$ has type $A_i$, the term $M$ is well-typed with type $A$". The list on the left is the **context**, usually written $\Gamma$. We need it because the type of a term depends on the types of its free variables: $x\,y$ has type $B$ if $x : A \to B$ and $y : A$, but has no type at all if $x : A \times B$.

(This is the same idea as last week: $M : A$ is a judgement *about* the term, not a proposition we can ask about.)

### The typing rules

A judgement is valid if it can be built using these rules. Read each one as "if the things above the line are derivable, so is the thing below".

$$
\dfrac{}{\Gamma,\, x : A \vdash x : A}\ (\text{var})
\qquad
\dfrac{\Gamma \vdash M : A \to B \qquad \Gamma \vdash N : A}{\Gamma \vdash M\,N : B}\ (\text{app})
\qquad
\dfrac{\Gamma,\, x : A \vdash M : B}{\Gamma \vdash \lambda x^A.M : A \to B}\ (\text{abs})
$$

$$
\dfrac{\Gamma \vdash M : A \qquad \Gamma \vdash N : B}{\Gamma \vdash \langle M, N\rangle : A \times B}\ (\text{pair})
\qquad
\dfrac{\Gamma \vdash M : A \times B}{\Gamma \vdash \pi_1 M : A}\ (\pi_1)
\qquad
\dfrac{\Gamma \vdash M : A \times B}{\Gamma \vdash \pi_2 M : B}\ (\pi_2)
\qquad
\dfrac{}{\Gamma \vdash * : 1}\ (*)
$$

In words:

* **(var)**: if we've assumed $x : A$, then $x : A$.
* **(app)**: a function $A \to B$ applied to an $A$ gives a $B$.
* **(abs)**: if $M : B$ using a variable $x : A$, then $\lambda x^A.M$ is a function $A \to B$. Notice that $x$ *leaves the context*: it's now bound.
* **(pair)**, **($\pi_1$)**, **($\pi_2$)**: build pairs and take them apart.
* **($*$)**: $*$ always has type $1$.

A useful observation: there's **exactly one rule for each kind of term**. So if you're given a term and asked to type it, you can work from the bottom up and there's never any choice about which rule to use next. The only real choice is what types to give the variables.

### Example

Let's show

$$\vdash\ \lambda x^A.\lambda f^{A \to B}.\, f\,x \ :\ A \to (A \to B) \to B.$$

This is the function "take an $a$ and a function $f$, and apply $f$ to $a$". Write $\Gamma$ for the context $x : A,\ f : A \to B$. The derivation is

$$
\dfrac{
  \dfrac{
    \dfrac{
      \dfrac{}{\Gamma \vdash f : A \to B}\ (\text{var})
      \qquad
      \dfrac{}{\Gamma \vdash x : A}\ (\text{var})
    }{\Gamma \vdash f\,x : B}\ (\text{app})
  }{x : A \vdash \lambda f^{A \to B}.\, f\,x : (A \to B) \to B}\ (\text{abs})
}{\vdash \lambda x^A.\lambda f^{A \to B}.\, f\,x : A \to (A \to B) \to B}\ (\text{abs})
$$

It's easiest to build these bottom-up:

1. The term is a $\lambda$, so the last rule must be (abs). Move $x : A$ into the context.
2. Again a $\lambda$, so (abs) again. Move $f : A \to B$ into the context.
3. Now we need $\Gamma \vdash f\,x : B$. That's an application, so (app). We need $f : A \to B$ and $x : A$.
4. Both are in $\Gamma$, so (var) finishes both branches.

Not everything types. For example, $\lambda x.\, x\,x$ has no type: for $x\,x$ to make sense, $x$ would need to have some type $A \to B$ *and* the type $A$ at the same time, and no simple type equals a type containing itself. That self-application is exactly the shape of Russell's paradox from last week.

## 2. Intuitionistic (Constructive) Logic

Here's the puzzle that motivates everything. Look at these types and ask whether there's a closed term (no free variables) of each:

| Type | Closed term? |
| --- | --- |
| $(A \times B) \to A$ | $\lambda x^{A \times B}.\, \pi_1 x$ |
| $A \to B \to (A \times B)$ | $\lambda x^A.\lambda y^B.\langle x, y\rangle$ |
| $(A \to B) \to (B \to C) \to (A \to C)$ | $\lambda f.\lambda g.\lambda a.\, g\,(f\,a)$ |
| $A \to (A \times B)$ | none |
| $(A \to C) \to C$ | none |

Now replace $\to$ by $\Rightarrow$ and $\times$ by $\land$. The types with a term turn out to be exactly the ones that are *tautologies*, and the others aren't. That's not a coincidence, and it's what Curry–Howard explains.

But there's a catch: the right logic to match up with is **intuitionistic** logic, not the classical logic we normally use.

### What's the difference?

In classical logic, every statement is either true or false, whether or not we know which. Intuitionistic logic instead asks: *what counts as a proof?* A proof of

* $A \land B$ is a proof of $A$ together with a proof of $B$;
* $A \Rightarrow B$ is a *method* turning any proof of $A$ into a proof of $B$;
* $\exists x.\, P(x)$ is a specific $x$ together with a proof of $P(x)$.

(This is called the Brouwer–Heyting–Kolmogorov interpretation, and you should already see the $\lambda$-calculus hiding in it: pairs, functions, and last week's $\Sigma$-types.)

Under this reading, two classical principles stop being automatic:

* **Excluded middle**: $A \lor \neg A$. To prove this constructively, you'd have to either prove $A$ or refute it, for *any* $A$. We can't do that in general.
* **Proof by contradiction**: if $\neg A$ leads to a contradiction, conclude $A$. Constructively, refuting $\neg A$ only proves $\neg\neg A$, which is weaker.

The upshot is that existence proofs must actually *construct* the thing. A classic example of a non-constructive proof: "there exist irrational $a, b$ with $a^b$ rational. Either $\sqrt2^{\sqrt2}$ is rational (take $a = b = \sqrt2$) or it isn't (take $a = \sqrt2^{\sqrt2}$, $b = \sqrt2$)." This is a perfectly good classical proof, but it doesn't tell you which pair works.

Intuitionistic logic doesn't say excluded middle is *false*. It just doesn't assume it. So it proves fewer things than classical logic, but every proof carries more information. In particular, a proof can be turned into an algorithm, and that's precisely the $\lambda$-term.

Even in the tiny fragment we're using (only $\to$, $\land$, $\top$), there are classical tautologies that have no proof. A standard one is **Peirce's law**:

$$((A \to B) \to A) \to A.$$

Check with a truth table that it's a classical tautology. You won't find a closed $\lambda$-term of that type, and correspondingly it isn't provable intuitionistically.

## 3. Natural Deduction

We now formalise intuitionistic proofs, using only the connectives $\to$, $\land$ and $\top$ (true):

$$A, B ::= \alpha \ \mid\ A \to B \ \mid\ A \land B \ \mid\ \top$$

where $\alpha$ ranges over atomic propositions. The proof system is Gentzen's **natural deduction** (1935). A proof is a tree called a **derivation**.

Selinger first presents derivations with assumptions at the leaves, then (Section 6.4) an equivalent version that's easier to reason about, where every line carries its assumptions with it. We'll use the second version. A **judgement** is

$$x_1 : A_1,\ \dots,\ x_n : A_n \ \vdash\ B$$

meaning "$B$ follows from the assumptions $A_1, \dots, A_n$". Each assumption has a label $x_i$ so we can keep track of which one we used.

### The six rules

Apart from the axiom, each rule belongs to one connective, and is either an **introduction** rule (how to prove it) or an **elimination** rule (how to use it).

$$
\dfrac{}{\Gamma,\, x : A \vdash A}\ (\text{ax}_x)
\qquad
\dfrac{}{\Gamma \vdash \top}\ (\top\text{-I})
$$

$$
\dfrac{\Gamma \vdash A \qquad \Gamma \vdash B}{\Gamma \vdash A \land B}\ (\land\text{-I})
\qquad
\dfrac{\Gamma \vdash A \land B}{\Gamma \vdash A}\ (\land\text{-E}_1)
\qquad
\dfrac{\Gamma \vdash A \land B}{\Gamma \vdash B}\ (\land\text{-E}_2)
$$

$$
\dfrac{\Gamma,\, x : A \vdash B}{\Gamma \vdash A \to B}\ (\to\text{-I}_x)
\qquad
\dfrac{\Gamma \vdash A \to B \qquad \Gamma \vdash A}{\Gamma \vdash B}\ (\to\text{-E})
$$

1. **Axiom**: you may conclude anything you've assumed.
2. **$\land$-introduction**: to prove $A \land B$, prove $A$ and prove $B$.
3. **$\land$-elimination**: from $A \land B$ you may conclude $A$, and you may conclude $B$.
4. **$\top$-introduction**: $\top$ is always provable. (There's no elimination rule: knowing $\top$ tells you nothing.)
5. **$\to$-introduction**: to prove $A \to B$, assume $A$ and prove $B$. The assumption $x : A$ is then **discharged**, so it no longer appears on the left. This is how we get rid of temporary assumptions.
6. **$\to$-elimination** (*modus ponens*): from $A \to B$ and $A$, conclude $B$.

A formula counts as **proved** when we can derive it with *no* assumptions, i.e. $\vdash A$.

### Example

Let's prove that "and" is commutative:

$$\vdash\ A \land B \to B \land A.$$

Informally: "Assume $A \land B$. Then $B$ holds (second part) and $A$ holds (first part), so $B \land A$." Formally:

$$
\dfrac{
  \dfrac{
    \dfrac{
      \dfrac{}{x : A \land B \vdash A \land B}\ (\text{ax}_x)
    }{x : A \land B \vdash B}\ (\land\text{-E}_2)
    \qquad
    \dfrac{
      \dfrac{}{x : A \land B \vdash A \land B}\ (\text{ax}_x)
    }{x : A \land B \vdash A}\ (\land\text{-E}_1)
  }{x : A \land B \vdash B \land A}\ (\land\text{-I})
}{\vdash A \land B \to B \land A}\ (\to\text{-I}_x)
$$

Again, build it bottom-up:

1. The goal is an implication, so use ($\to$-I): assume $x : A \land B$ and aim for $B \land A$.
2. The goal is a conjunction, so use ($\land$-I): we need $B$ and $A$ separately.
3. Each of those comes from the assumption $A \land B$ by ($\land$-E), and the assumption itself comes from (ax).

Compare this with the typing derivation in Section 1. The shape is the same, and that's the whole point.

## 4. The Curry–Howard Isomorphism

### The idea

Put the two sets of rules next to each other and replace $\times$ with $\land$ and $1$ with $\top$:

| $\lambda$-calculus | Logic |
| --- | --- |
| type $A \to B$, $A \times B$, $1$ | formula $A \to B$, $A \land B$, $\top$ |
| context $x_1 : A_1, \dots, x_n : A_n$ | labelled assumptions |
| term $M$ with $\Gamma \vdash M : A$ | derivation of $\Gamma \vdash A$ |
| (var) | (ax) |
| (abs) | ($\to$-I) |
| (app) | ($\to$-E) |
| (pair) | ($\land$-I) |
| ($\pi_1$), ($\pi_2$) | ($\land$-E$_1$), ($\land$-E$_2$) |
| ($*$) | ($\top$-I) |

The rules are *literally the same*, except the logic rules forget the term. So:

* **Types and formulas** are the same thing (identify basic types with atomic formulas).
* **Terms and derivations** are the same thing. A typing derivation for $M$ is a natural-deduction proof with the term written in. Conversely, a natural-deduction proof tells you how to build a term, one rule at a time.

The theorem says these translations exist and are inverse to each other (up to renaming bound variables).

**Theorem (Curry–Howard).** There is a translation from derivations of $x_1 : A_1, \dots, x_n : A_n \vdash B$ to terms $M$ such that

$$x_1 : A_1,\ \dots,\ x_n : A_n \ \vdash\ M : B$$

is a valid typing judgement, and a translation back, and the two are mutually inverse up to $\alpha$-equivalence.

In particular: **$A$ is provable if and only if there is a closed term of type $A$.** This is what explains the puzzle at the start of Section 2.

### How the proof works

We define the term $M$ by **recursion on the derivation**, and *at the same time* prove by **induction on the derivation** that the term we built has the right type.

The derivation was built by one of six rules, so there are six cases. In each case:

1. The last rule used had some sub-derivations above it (zero, one or two).
2. By recursion, each sub-derivation already has a term, and by the induction hypothesis that term is well-typed.
3. We combine them using the matching term-former, and the matching typing rule shows the result is well-typed.

Each case is about one line, because each logic rule lines up with exactly one typing rule.

### The proof in full

Fix a derivation of $\Gamma \vdash B$. We go by cases on the last rule used.

**Case 1: (ax).** The derivation is

$$\dfrac{}{\Gamma,\, x : A \vdash A}\ (\text{ax}_x)$$

Let $M = x$. Then $\Gamma, x : A \vdash x : A$ by (var).

**Case 2: ($\land$-I).** The derivation ends with

$$\dfrac{\begin{matrix}\vdots \\ \Gamma \vdash A\end{matrix} \qquad \begin{matrix}\vdots \\ \Gamma \vdash B\end{matrix}}{\Gamma \vdash A \land B}\ (\land\text{-I})$$

Let $P$ and $Q$ be the terms for the two sub-derivations. By the induction hypothesis, $\Gamma \vdash P : A$ and $\Gamma \vdash Q : B$. Let $M = \langle P, Q\rangle$. Then $\Gamma \vdash \langle P, Q\rangle : A \times B$ by (pair).

**Case 3: ($\land$-E$_1$).** The derivation ends with

$$\dfrac{\begin{matrix}\vdots \\ \Gamma \vdash A \land B\end{matrix}}{\Gamma \vdash A}\ (\land\text{-E}_1)$$

Let $P$ be the term for the sub-derivation. By the induction hypothesis, $\Gamma \vdash P : A \times B$. Let $M = \pi_1 P$. Then $\Gamma \vdash \pi_1 P : A$ by ($\pi_1$). The case ($\land$-E$_2$) is the same, using $\pi_2$.

**Case 4: ($\top$-I).** The derivation is

$$\dfrac{}{\Gamma \vdash \top}\ (\top\text{-I})$$

Let $M = *$. Then $\Gamma \vdash * : 1$ by ($*$).

**Case 5: ($\to$-I).** The derivation ends with

$$\dfrac{\begin{matrix}\vdots \\ \Gamma,\, x : A \vdash B\end{matrix}}{\Gamma \vdash A \to B}\ (\to\text{-I}_x)$$

Let $P$ be the term for the sub-derivation. By the induction hypothesis, $\Gamma, x : A \vdash P : B$. Let $M = \lambda x^A.P$. Then $\Gamma \vdash \lambda x^A.P : A \to B$ by (abs).

This is the interesting case: *discharging an assumption is $\lambda$-abstraction*. The label $x$ on the assumption becomes the bound variable.

**Case 6: ($\to$-E).** The derivation ends with

$$\dfrac{\begin{matrix}\vdots \\ \Gamma \vdash A \to B\end{matrix} \qquad \begin{matrix}\vdots \\ \Gamma \vdash A\end{matrix}}{\Gamma \vdash B}\ (\to\text{-E})$$

Let $P$ and $Q$ be the terms for the two sub-derivations. By the induction hypothesis, $\Gamma \vdash P : A \to B$ and $\Gamma \vdash Q : A$. Let $M = P\,Q$. Then $\Gamma \vdash P\,Q : B$ by (app).

*Modus ponens is function application.*

That covers every rule, so every derivation gives a well-typed term. $\blacksquare$

**The other direction.** Given a valid typing judgement $\Gamma \vdash M : A$, we build a derivation of $\Gamma \vdash A$ by recursion on the typing derivation, sending each typing rule to its logical partner:

$$(\text{var}),\ (\text{abs}),\ (\text{app}),\ (\text{pair}),\ (\pi_1),\ (\pi_2),\ (*) \ \longmapsto\ (\text{ax}),\ (\to\text{-I}),\ (\to\text{-E}),\ (\land\text{-I}),\ (\land\text{-E}_1),\ (\land\text{-E}_2),\ (\top\text{-I}).$$

In practice, this just means erasing the terms. Selinger doesn't spell this direction out, and neither will we: it's the same six cases read backwards. Going one way and then the other gets you back where you started (up to renaming bound variables), which is why it's an *isomorphism* and not just a correspondence.

### Example: running the translation

Take the proof of $A \land B \to B \land A$ from Section 3 and translate it from the top down:

* The two (ax$_x$) leaves both become $x$.
* ($\land$-E$_2$) and ($\land$-E$_1$) turn them into $\pi_2 x$ and $\pi_1 x$.
* ($\land$-I) pairs them: $\langle \pi_2 x, \pi_1 x\rangle$.
* ($\to$-I$_x$) abstracts over $x$: $\lambda x^{A \times B}.\langle \pi_2 x, \pi_1 x\rangle$.

So the proof *is* the program

$$\lambda x^{A \times B}.\, \langle \pi_2 x,\ \pi_1 x\rangle \ :\ A \times B \to B \times A,$$

i.e. "swap the two components of a pair". (This is Selinger's Exercise 26(b).)

## Looking Ahead

Section 6.6 adds $\beta$-reduction (running programs) to the simply-typed $\lambda$-calculus, and the sections after it ask what that means on the logic side. It turns out to be *simplifying a proof* (removing a detour where you introduce a connective and immediately eliminate it). That's a nice thing to read before next week.

## Questions to Think About

* Under Curry–Howard, what does the identity function $\lambda x^A.x$ prove?
* Try to find a closed term of type $A \to (A \times B)$. Where do you get stuck? What does that say about the formula $A \to A \land B$?
* $A \to A \to A$ has two different closed terms. What does that say about proofs of $A \to A \to A$?
* Peirce's law $((A \to B) \to A) \to A$ is classically true. Can you convince yourself there's no $\lambda$-term of that type?
