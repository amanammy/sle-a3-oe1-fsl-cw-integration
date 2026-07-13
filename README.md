# SLE Assignment 3 — Option OE.1

Cardelli & Wegner's polymorphism taxonomy, and how it fits into FSL.

## What I found

Cardelli & Wegner (1985) classify polymorphism into a small tree:

```
              Polymorphism
             /            \
      Universal          Ad-hoc
      /      \           /     \
Parametric  Inclusion  Overloading  Coercion
```

FSL has already read this paper. Its `ce.ttl` contains
`ParametricPolymorphism`, `AdHocPolymorphism` and `Subtyping` — which is
what C&W call inclusion polymorphism.

But there is no `Polymorphism` root, no `UniversalPolymorphism`, no
`Overloading` and no `Coercion`. None of them appear anywhere in the repo.
And the concepts that are there sit completely flat, with no relation
between them.

So FSL took the vocabulary but not the shape. 

## Why the shape couldn't be taken

FSL's concepts are individuals, grouped into 18 flat facets. There is no
property anywhere in `tbox.ttl` that says one concept is a *kind of*
another. I went through all 43 object properties.

Two come close and neither works:

- `hasComponent` is part-of. FSL says `Closures hasPrimaryComponent
  FirstClassFunctions`. But parametric polymorphism isn't a *part of*
  universal polymorphism — it *is a kind of* it.
- `hasDominantForm` is per-language, inside a reified assertion. It says
  "for Haskell, evaluation is lazy". It never says, in general, that lazy
  evaluation is a kind of evaluation strategy.

C&W's tree is made of nothing but kind-of edges, so it can't currently be
expressed in FSL.

This isn't really about typing. 15 of FSL's 18 concept facets have a
general concept sitting flat beside its own specialisations —
`Abstraction` next to `DataAbstraction` and `FunctionAbstraction`,
`EvaluationStrategy` next to `LazyEvaluation` and `EagerEvaluation`,
`Scope` next to `LexicalScope`. C&W didn't cause this. It just made it
visible.

## What actually breaks

`pe.ttl` says Java natively supports `AdHocPolymorphism`.

But ad-hoc polymorphism *is* overloading or coercion — those are its only
two kinds, and FSL has neither. So that assertion can't say which one it
means.

To be fair to FSL: this isn't a missing entry. Its `profilingPolicy` says
concepts are asserted selectively, not exhaustively, and that's deliberate.
The problem is that the vocabulary to refine it doesn't exist. There's
nothing you could write.

## The proposal

`fsl-cw-integration.ttl` imports FSL's tbox and adds:

- **one property**, `tbox:refines` (LanguageConcept → LanguageConcept),
  meaning "is a kind of". Written in FSL's own style — PropertyEntity,
  domain, range, inverse. Transitive, so you can ask for everything under
  `Polymorphism` and get the leaves back. Deliberately not asymmetric or
  irreflexive: OWL 2 DL doesn't allow that together with transitivity.
  (FSL makes the opposite call on `hasPart` — asymmetric and irreflexive,
  but not transitive.)
- the four concepts FSL is missing: `Polymorphism`,
  `UniversalPolymorphism`, `Overloading`, `Coercion`.
- a link from `Subtyping` to `UniversalPolymorphism`. C&W say outright
  that subtyping is an instance of inclusion polymorphism, and FSL never
  relates it to polymorphism at all.
- refined assertions for Java's and Haskell's ad-hoc polymorphism, using
  FSL's **existing** `LanguageSupportAssertion` machinery. Java's dominant
  form is overloading, via method overloading. Haskell's is also
  overloading, but via type classes — and FSL already had
  `TypeClassBasedMechanism`, it just had nothing in the typing branch to
  attach it to.
- two retrofits in other facets (`LazyEvaluation refines
  EvaluationStrategy`, `LexicalScope refines Scope`), to show the property
  isn't a one-off for this paper.

With that, C&W's whole tree is six triples. The facets stay flat.

## What it doesn't do

`refines` gives you C&W's hierarchy but can't enforce their actual claim —
that universal and ad-hoc polymorphism are disjoint. You can't declare
individuals disjoint the way you can classes.

Getting that would mean turning the concepts into OWL classes and using
`rdfs:subClassOf`. That works, but it restructures FSL's concept model
instead of adding to it. That trade-off seemed worth stating rather than
hiding.

## Checks

Merges into the full FSL ontology (tbox, ce, pe, fe, le, ae, te):
5028 triples, no domain/range violations, stays within OWL 2 DL.

## Sources

- FSL: https://github.com/softlang/fsl
- Cardelli, L. and Wegner, P. *On Understanding Types, Data Abstraction,
  and Polymorphism.* ACM Computing Surveys 17(4), 1985. (FSL reference [82])
