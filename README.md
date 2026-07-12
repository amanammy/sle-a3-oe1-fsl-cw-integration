# SLE Assignment 3 — Option OE.1

This repo contains a small OWL/Turtle file that shows how Cardelli & Wegner's
1985 classification of polymorphism could be integrated into FSL's Phase 7
(SL Concept Coverage).

FSL currently has a "typing" concept branch planned but not filled in, and
explicitly cites Cardelli & Wegner as one of the existing classification
schemes it hasn't yet integrated. This file is a proposal for what that
integration could look like.

## What's in the file

`fsl-cw-integration.ttl` adds a `TypingConcept` class under FSL's existing
`LanguageConcept` class, with four subclasses matching Cardelli & Wegner's
taxonomy:

- Parametric polymorphism
- Inclusion polymorphism
- Overloading
- Coercion

Two of the four are actually connected to a real FSL entity: Java, which
FSL's own paper already discusses as an example language. Java supports
both parametric polymorphism (generics) and inclusion polymorphism
(subtyping/inheritance), using FSL's existing `supports` property. System
F is also included, correctly typed as a formal entity (not a language
entity, which matters for which properties can connect to it), linked via
FSL's existing `uses` property to show it's the calculus that formalizes
parametric polymorphism.

Overloading and coercion are defined as classes but left unpopulated on
purpose — FSL doesn't currently have a clean existing example entity for
either, so populating them here would mean inventing something not part
of the actual FSL ontology. That's noted as future work rather than
filled in artificially.

This is a standalone illustration, not a fork of the real FSL ontology — it
reuses FSL's naming and property conventions but lives in its own namespace.

## Sources

- Lämmel, R. et al. *Foundations of Software Languages* (FSL), draft.
- Cardelli, L.; Wegner, P. *On Understanding Types, Data Abstraction, and
  Polymorphism*. ACM Computing Surveys, 1985.
