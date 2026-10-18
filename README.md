# Formation Sketch Checker

Automated guardrails for the Seifert–Brieskorn / 2-adic spectral circle formation sketch.

## What it checks

The GitHub Action `.github/workflows/check-sketch.yml` fails the build if `formation_sketch_seifert_spectral.md` contains any of the following overclaims:

- Unscoped assertion that a 3-variable Brieskorn link is a topological sphere
- Spectrum described as filling circles (finite matrix only has eigenvalues *on* the circles)
- Constant per-step weight magnitude √2 (the product around the cycle has magnitude √2)
- Haar uniqueness or Lyapunov exponent stated as fact rather than expected / open
- Mismatched Casson rows (Σ(3,4,5), Σ(3,5,7)) marked as agreeing

## Current certified facts

- Pairwise coprimality ⇒ integral homology 3-sphere (via |O| = 1)
- Casson equality (Milnor |σ|/8 = SU(2) #ℛ*/2) holds with `rfl` only for Σ(2,3,5), Σ(2,3,7), Σ(2,3,11), Σ(2,5,7)
- Eigenvalues of the finite Collatz relation matrix lie on the circles of radius 2^{2^{-(m-1)}}

## Open edges

See the note itself. The Action does not yet pull live Lean sources; it encodes the known constraints statically so the prose cannot drift back into the previously corrected errors.

## Next steps

- Wire a job that clones `sneed-and-feed/original-research` and greps for the actual `casson_su2_eq_brieskorn_*` theorems before accepting new table rows.
- Add a Lean CI check that the four certified equalities still hold.
