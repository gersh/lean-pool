# Research map for Poincaré's Chapter VI

Status: exploratory research, not a claim of a completed formalization and not intended for a
Lean Pool pull request in its present form.

This note separates three questions that are easy to conflate:

1. What is proved in Chapter VI of volume I of *Les méthodes nouvelles de la mécanique
   céleste*?
2. Which individual reductions have been checked in Lean on this branch?
3. What would be required for a faithful modern proof of the decisive argument?

The primary text is [Chapter VI, §§90–103][chapter-vi]. The page-level facsimiles are linked
below where the printed formula matters.

## Bottom line

The branch does **not** formalize Poincaré's Chapter VI proof. It verifies several exact algebraic
and formal-series subarguments. The central analytic and geometric steps remain open:

- constructing Poincaré's actual two-variable perturbing function as a holomorphic or
  multivalued analytic object;
- proving which candidate singularities pinch the integration cycle and are genuine rather than
  apparent;
- deriving the local logarithmic expansion from a convergent Weierstrass preparation and a
  parameter-dependent contour;
- proving a Darboux remainder estimate strong enough to give eventual nonvanishing;
- justifying the parameter-rank and projective intersection argument in §§102–103, including
  multiplicities, points at infinity, and no-common-component hypotheses.

This is not merely a matter of filling routine Lean library gaps. At the end of §98 Poincaré says
that he has only sketched the discussion and calls for a complete analytic study of the different
branches of `Φ(z)`. A faithful completion therefore contains mathematical reconstruction beyond
transcription of the 1892 text.

## Passage-to-formalization inventory

| Source | Role in the printed argument | Current branch | What is still needed |
| --- | --- | --- | --- |
| §90 | Full three-body Hamiltonian `F = F₀ + μF₁`; isolate the principal mutual-distance term | The existing project has a restricted circular problem and a first mass derivative, not this full Hamiltonian | Decide whether the target is the full problem in Poincaré's variables or the circular restricted theorem; define the exact source Hamiltonian and prove the coordinate/mass expansion |
| §§91–92 | Osculating variables, the first homological equation, and invariance under a coordinate change | The product-rule/homological algebra has restricted analogues | Formalize the source coordinate maps, domains, symplecticity, and the precise notion of a uniform first integral used in Chapter V–VI |
| §93 | Darboux's one-variable coefficient estimates | `eventually_ne_zero_of_tendsto_div_one` proves only the final elementary nonvanishing implication | A singularity-analysis theorem with explicit hypotheses, competing boundary singularities, and a controlled analytic remainder |
| §94 | Convert coefficients on `(m₁,m₂)=(an+b,cn+d)` to coefficients of one Laurent series `Φ(z)` | `ChapterVILatticeReduction.lean` proves the affine-lattice reindexing; `ChapterVIContour.lean` proves finite and absolutely summable Laurent coefficient extraction | Define the actual Fourier series and prove its holomorphic convergence on an annulus, allowing all substitutions, sum/integral interchanges, and branch choices |
| §95 | Candidate singularities arise when moving singularities of the integrand obstruct contour deformation | No source-level analytic theorem | A parameterized contour-deformation theorem for multivalued algebraic integrands; modern language suggests vanishing cycles and Picard–Lefschetz theory |
| §96 | Algebraic equations for candidate singularities | `ChapterVISingularityAlgebra.lean` checks selected half-angle factorizations, reciprocal symmetries, a discriminant, and `z ↦ z⁻¹` | Formalize all collision equations from the actual Kepler parametrization and prove equivalence without losing roots while clearing denominators |
| §§97–98 | Decide which candidates are admissible and which singularity lies on the boundary of the Laurent annulus | Not formalized | Construct the relevant Riemann surface/cycle, compute monodromy or vanishing-cycle intersection, and prove the required parameter regions. Poincaré explicitly says this discussion is only sketched |
| §99 | Localize at a pinch and prepare the double zero as `ψ=((t-h)²+k)ψ₁` | `ChapterVIWeierstrass.lean` proves the analogous statement for nested **formal** power series | Analytic Weierstrass preparation for the actual convergent germ, nondegeneracy of the double zero, compatible square-root branches, and the contour localization |
| §100 | Integrate the prepared local model to obtain `Φ₂+Φ₃ log(z-z₀)` and apply Darboux | `ChapterVIDarboux.lean` proves the exact coefficients of a model logarithm and an abstract asymptotic-to-nonvanishing step | Derive the logarithmic expansion of the actual integral; prove the leading factor is nonzero; bound the holomorphic and higher-order terms, including all equally dominant singularities |
| §101 | Astronomical example (the Pallas inequality) | Not formalized | Optional for nonintegrability; relevant only if the project also verifies the numerical application |
| §102 | A uniform integral would constrain the singular points to depend on too few parameters | Only a conditional restricted-problem interface in `ChapterVI.lean` | Formalize the Chapter V input, analytic dependence/enumeration of singular roots, Jacobian-rank reasoning, and passage from coefficient relations to singular-locus relations |
| §103 | Count 24 finite singular points and contradict the rank constraint using two degree-six curves with 44 counted intersections | Not formalized | Projective closure, Bézout with no common component, local intersection multiplicities at the origin and infinity, persistence under deformation, and the final moving-ellipse contradiction |

## What the current Lean files actually establish

The source-facing files added after the standalone-project commit are deliberately small lemmas:

- `ChapterVILatticeReduction.lean`: exact lattice/shear identities for finite tables and summable
  series.
- `ChapterVIContour.lean`: normalized circle integrals extract finite Laurent coefficients, and
  extract an infinite Laurent coefficient under an explicit weighted summability hypothesis.
- `ChapterVISingularityAlgebra.lean`: selected polynomial identities from §96 and the reciprocal
  symmetries that they imply.
- `ChapterVIWeierstrass.lean`: formal Weierstrass preparation over `ℂ⟦z-z₀⟧` followed by completing
  a monic quadratic square.
- `ChapterVIDarboux.lean`: the model logarithm's coefficients and the conditional implication from
  a nonzero Darboux asymptotic to eventual coefficient nonvanishing.
- `ChapterVI.lean`: a passage-by-passage status statement and a conditional interface from the
  missing Darboux nonvanishing result to the project's restricted nonintegrability theorem.

None of these files constructs Poincaré's `Φ`, identifies its genuine boundary singularity, or
proves the missing asymptotic hypothesis.

## Source problems found in the facsimile

These should be settled in a mathematical note, with expert review, before Lean is asked to choose
a corrected statement.

### Equation (10), p. 290

The [facsimile of p. 290][page-290] prints

```text
2x - sin(φ)(x² - 1) = -2βx.
```

But the immediately preceding equation `1 - sin(φ) cos(u) = ±β`, together with the same
half-angle substitution used throughout the section, gives an `x² + 1` term. The printed
`x² - 1` equation is also not reciprocal, while the next paragraph explicitly says that equations
(1), (9), and (10) are reciprocal. The branch formalizes the `x² + 1` correction. This is strong
internal evidence of a typographical error, but it should be described as a correction rather than
silently attributed to the printed source.

### The leading factor `θ₀,₀`, p. 323

The [facsimile of p. 323][page-323] first states

```text
θ / sqrt((t-h)²+k) = t^(ad-bc-1) z^(-d/c) / sqrt(Δ)
```

and then prints `θ₀,₀` as the monomial factor times `(1/2) ∂²Δ/∂t²`. If
`Δ = (1/2)Δ_tt(t-t₀)² + …` and the prepared monic factor begins with `(t-t₀)²`, the displayed
identity instead makes `θ₀,₀` proportional to `sqrt(2/Δ_tt)`, up to the chosen square-root branch.
Thus the printed formula appears dimensionally and algebraically inconsistent with its preceding
line. This needs an independent derivation and a search for corrigenda or later treatments; it is
not yet encoded in Lean.

## The hard mathematical core

### 1. Genuine versus apparent pinches (§§95–99)

Solving `ψ=∂ψ/∂t=0` only locates a discriminant. It does not prove that the original integration
cycle is pinched. Poincaré distinguishes genuine and apparent singularities by how sheets of the
Riemann surface and the contour behave. A modern reconstruction should state:

1. a family of algebraic or analytic curves over the parameter `z`;
2. the cycle representing the coefficient integral;
3. the discriminant and its simple points;
4. the local vanishing cycle at each discriminant point;
5. a criterion, normally an intersection number, for whether monodromy changes the integration
   cycle and hence produces an actual logarithmic singularity.

This is the conceptual bottleneck. The local quadratic factorization by itself does not establish
the nonzero logarithmic coefficient.

### 2. From a local logarithm to a coefficient theorem (§100)

The needed statement is more precise than “a logarithm has coefficients `-z₀⁻ⁿ/n`.” One must
prove that on the relevant annulus:

```text
Φ(z) = H(z) + G(z) log(1-z/z₀),    G(z₀) ≠ 0,
```

with all other singularities on the same modulus either absent or included in the asymptotic, and
with a remainder smaller than the leading term. Multiple boundary singularities can cancel on a
subsequence, so eventual nonvanishing does not follow from a single local calculation unless the
global boundary-singularity statement rules this out.

### 3. The intersection count (§103)

Poincaré takes `P=x²y²Δ` of degree six, reduces the derivative equation on `P=0` to a degree-seven
curve `R=0`, and counts `6·7=42` intersections. He subtracts multiplicities `2` at the origin and
`8+8` in the two directions at infinity to obtain 24 finite singular points. He then compares `P`
with an infinitesimally varied degree-six curve `P'`: the claimed common intersections have total
multiplicity `24+4+8+8=44>36`, so Bézout would force a common component (the text says the curves
coincide), contradicting relative motion of the ellipses.

A rigorous version must make explicit:

- the base field and projective homogenizations;
- generic parameter assumptions and exclusions caused by cleared denominators;
- why `P` and `R` have no common component;
- each local intersection multiplicity, not merely a branch count;
- why the 24 finite points are distinct or how their multiplicities are tracked;
- why the infinitesimal deformation may be replaced by an actual neighboring algebraic curve;
- why a common component implies the stronger invariance conclusion used geometrically.

The arithmetic `42-2-8-8=24` and `44>36` is easy to formalize. The mathematical content lies in
proving that those numbers are the correct local intersection multiplicities.

## A second primary-source route: Poincaré 1897

Poincaré's later paper [*Sur les périodes des intégrales doubles et le développement de la
fonction perturbatrice*][poincare-1897] deserves its own branch of the roadmap. It treats Fourier
coefficients as periods of algebraic double integrals, proves finite reduction relations, and
argues that the period functions satisfy linear differential equations with rational (after
clearing denominators, polynomial) coefficients. For zero eccentricities it reduces the
coefficients to at most five transcendental functions.

This suggests a Picard–Fuchs or holonomic formalization:

```text
algebraic family → finite de Rham/period module → rational differential system
                 → coefficient recurrences → certified nonvanishing question
```

It may be better suited to finite computation than reconstructing every contour drawing in
§§97–98. It is **not yet a replacement proof**: finite recurrences do not automatically prove the
specific nonvanishing or parameter-rank statement needed for nonintegrability, and Poincaré notes
that the exponential factor for mean anomalies prevents immediate application of the algebraic
case. The research task is to determine whether the 1897 differential system can supply a
checkable certificate for the needed coefficients.

## Relation to modern results

Yagasaki's [new proof of Poincaré's restricted result][yagasaki-classical] explicitly describes
the original proof as complicated and unclear and replaces the perturbing-function calculation
with a modern meromorphic nonintegrability criterion near resonant periodic orbits. His
[stronger fixed-mass result][yagasaki-fixed-mass] uses Morales–Ramis type differential-Galois
obstructions and has a different conclusion and proof architecture.

These should be separate roadmap milestones:

1. a precisely scoped classical, small-mass, real-analytic result;
2. an optional reconstruction of Chapter VI as a historical/mathematical verification project;
3. the modern fixed-nonzero-mass meromorphic nonintegrability result.

Formalizing milestone 1 does not require claiming that milestone 2 is complete. Conversely,
verifying scattered Chapter VI calculations is valuable research but should not be marketed as a
proof of milestone 1 until the pinch, asymptotic, and rank arguments are closed.

## Recommended next steps on this branch

1. Freeze the public Lean Pool submission at the standalone classical project boundary; keep all
   Chapter VI reconstruction commits here until expert review.
2. Write a precise theorem statement for the missing pinch-to-logarithm result, independent of
   celestial-mechanics notation. Compare it against modern vanishing-cycle results before coding.
3. Re-derive the local factor `θ₀,₀` and ask a complex-analysis/celestial-mechanics expert to check
   both identified source corrections.
4. Formalize the exact Jacobian rescaling identity in §102; it is source-faithful algebra and does
   not pretend to solve the rank argument.
5. Prototype the §103 projective curves in a computer algebra system to verify degrees,
   homogenizations, exceptional factors, and local multiplicities before choosing Lean
   statements.
6. Study whether the 1897 Picard–Fuchs relations yield finite certificates compatible with the
   finite-computation infrastructure. Treat this as an alternative research route, not as a
   completed bridge.
7. Ask reviewers to evaluate the mathematical reconstruction document first. Open a formalization
   PR only when a self-contained theorem and its source correspondence are stable.

## Sources

- Henri Poincaré, [*Les méthodes nouvelles de la mécanique céleste*, volume I, Chapter VI
  (§§90–103)][chapter-vi], 1892.
- Henri Poincaré, [facsimile p. 290][page-290] and [facsimile p. 323][page-323].
- Henri Poincaré, [*Sur les périodes des intégrales doubles et le développement de la fonction
  perturbatrice*][poincare-1897], *Journal de mathématiques pures et appliquées* 5e série, 3
  (1897), 203–276.
- Kazuyuki Yagasaki, [*A new proof of Poincaré's result on the restricted three-body
  problem*][yagasaki-classical], arXiv:2111.11031.
- Kazuyuki Yagasaki, [*Nonintegrability of the restricted three-body
  problem*][yagasaki-fixed-mass], arXiv:2106.04925.

[chapter-vi]: https://fr.wikisource.org/wiki/Les_m%C3%A9thodes_nouvelles_de_la_m%C3%A9canique_c%C3%A9leste/Chap.06
[page-290]: https://fr.wikisource.org/wiki/Page:Henri_Poincar%C3%A9_-_Les_m%C3%A9thodes_nouvelles_de_la_m%C3%A9canique_c%C3%A9leste,_Tome_1,_1892.djvu/302
[page-323]: https://fr.wikisource.org/wiki/Page:Henri_Poincar%C3%A9_-_Les_m%C3%A9thodes_nouvelles_de_la_m%C3%A9canique_c%C3%A9leste,_Tome_1,_1892.djvu/335
[poincare-1897]: https://www.numdam.org/item/JMPA_1897_5_3__203_0.pdf
[yagasaki-classical]: https://arxiv.org/abs/2111.11031
[yagasaki-fixed-mass]: https://arxiv.org/abs/2106.04925
