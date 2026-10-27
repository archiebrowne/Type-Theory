# Type Theory

Material for the Autumn 26/27 DRP on Type Theory @ Imperial (work in progress!)

Contact: ajb421 [at] ic [dot] ac [dot] uk

## Reading Material:

* Russell's Paradox, Stanford Encyclopedia of Philosophy: https://plato.stanford.edu/entries/russell-paradox/, the paradox that started motivated a divergence from naive set theory. 
* Thorsten Altenkirch, Should Type Theory Replace Set Theory as the Foundation of Mathematics?: https://arxiv.org/abs/2111.06368, short and opinionated case for type theory over ZFC.
* The aboutlogic podcast: https://www.youtube.com/@aboutlogic, very approachable an interesting podcast about logic, with a lot about type theory.
* Andrej Bauer, Five Stages of Accepting Constructive Mathematics: https://math.andrej.com/2016/10/10/five-stages-of-accepting-constructive-mathematics/, maths without the law of excluded middle.
* Vladimir Voevodsky, The Origins and Motivations of Univalent Foundations: https://www.ias.edu/ideas/2014/voevodsky-origins, a Fields medallist explains why he started worrying about mistakes in his own proofs.
* Philip Wadler, Propositions as Types: https://homepages.inf.ed.ac.uk/wadler/papers/propositions-as-types/propositions-as-types.pdf, readable history of the Curry–Howard correspondence.
* Thorsten Altenkirch, Naive Type Theory: https://nottingham-repository.worktribe.com/output/1990007, informal introduction to type theory.
* Type Theory, Stanford Encyclopedia of Philosophy: https://plato.stanford.edu/entries/type-theory/, overview from Russell's ramified types.
* Daniel Grayson, An Introduction to Univalent Foundations for Mathematicians: https://arxiv.org/abs/1711.01477, how Voevodsky encoded ordinary maths in type theory. Written for working mathematicians.
* Mathematics in Lean: https://leanprover-community.github.io/mathematics_in_lean/, doing actual undergraduate maths in Lean and mathlib.
* Egbert Rijke, Introduction to Homotopy Type Theory: https://arxiv.org/abs/2212.11082, our main text. Part I covers dependent type theory from scratch, one lecture per chapter.
* Simon Thompson, Type Theory and Functional Programming: https://www.cs.kent.ac.uk/people/staff/sjt/TTFP/, older but clear account of Martin-Löf type theory from the programming side.
* Nordström, Petersson & Smith, Programming in Martin-Löf's Type Theory: https://www.cse.chalmers.se/research/group/logic/book/, the classic treatment of Martin-Löf's system. Free online.
* Rob Nederpelt & Herman Geuvers, Type Theory and Formal Proof: https://wsinrpn.win.tue.nl/book_type_theory.htm, slow, careful route from lambda calculus to the Calculus of Constructions.
* The HoTT Book: https://homotopytypetheory.org/book/, the standard reference from the 2012–13 IAS special year. Chapter 1 is approachable is most relevant for this reading group.
* Girard, Lafont & Taylor, Proofs and Types: http://www.paultaylor.eu/stable/Proofs+Types.html, proof theory of typed lambda calculi, including System F. Dense.
* Michael Shulman, Homotopy Type Theory: A Synthetic Approach to Higher Equalities: https://arxiv.org/abs/1601.05035, quite (maybe too) advanced.

## Lean and ITP's

(Dependent) Type Theory is the mathematical foundation of the Lean Theorem Prover. If you want to see how this works, then a good place to start is:
* Natural Number Game (Kevin Buzzard et al.): https://adam.math.hhu.de/#/g/leanprover-community/nng4, the game goes through how the natural numbers are defined in this theory, and how we can prove properties about them (mostly by a lot of induction!).

If you want to go further than the natural numbers, then:
* Theorem Proving in Lean 4: https://lean-lang.org/theorem_proving_in_lean4/, dependent type theory explained through Lean, with lots of examples.
* Kevin Buzzard, Formalising Mathematics: https://www.ma.imperial.ac.uk/~buzzard/xena/formalising-mathematics-2024/, notes from the Imperial course.

Agda is an older interactive theorem prover:
* Martín Escardó, Introduction to Univalent Foundations of Mathematics with Agda: https://arxiv.org/abs/1911.00580, univalent foundations developed in Agda code.
