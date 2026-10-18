# Formation Sketch: Discrete Resolution via Seifert–Brieskorn Bridges and 2-Adic Spectral Circles

**Source cores:** Machine-checked discrete results from sneed-and-feed/original-research (BrieskornBridge.lean, MilnorSignature.lean, BrieskornSU2CharacterVariety) and adelic-spectral-zeta (Spectral Circle Theorem).  
**Scope:** Algebraic cores only. Continuous asymptotics and global adelic models remain exploratory.  
**Date:** 2026-10-18 (corrected)

---

## 1. The Seifert–Brieskorn Bridge (Homology Sphere)

Pairwise coprime exponents \(p, q, r \ge 2\) make the Brieskorn singularity link
\[
\Sigma(p,q,r) = V(z_1^p + z_2^q + z_3^r = 0) \cap S^5
\]
an integral homology 3-sphere. This is the standard result: the first homology is cyclic of order \(|O|\), and pairwise coprimality forces \(|O| = 1\).

These are homology spheres, not topological spheres. \(\Sigma(2,3,5)\) is the Poincaré homology sphere; its fundamental group is the binary icosahedral group of order 120, so it is not homeomorphic to \(S^3\).

The Lean predicate `IsHomotopySphere` is a misleading name if it only checks the Seifert condition \(|O| = 1\). A clearer name is `IsHomologySphere`.

The predicate `brieskornSphereCondition` encodes the Brieskorn graph criterion (at least two isolated vertices). That criterion implies a topological sphere only for links of dimension \(2n-1\) with \(n \ge 4\). In three variables it holds under pairwise coprimality but carries no additional topological content; the homology-sphere conclusion follows from the Seifert order alone.

Machine-checked statement (0 sorry):

```lean
theorem brieskorn_seifert_bridge_3point (p q r : ℕ)
    (hpq : p.Coprime q) (hpr : p.Coprime r) (hqr : q.Coprime r) :
    Brieskorn.brieskornSphereCondition (Brieskorn.brieskornThreeExponents p q r) ∧
    ∃ (l0 : ℤ) (l : Fin 3 → ℤ), IsHomotopySphere (vec3 p q r) l0 l
```

A tighter bridge equates the Milnor fiber Casson invariant \(\lambda = |\sigma|/8\) with the SU(2) character-variety count \(\frac12 \#\mathcal{R}^*\) for the four cases that have been certified:

```lean
-- Equality theorems exist only for these four:
-- casson_su2_eq_brieskorn_2_3_5, _2_3_7, _2_3_11, _2_5_7
```

**Casson values (certified equality only):**

| Sphere     | σ (Milnor) | Milnor \(\lambda = |\sigma|/8\) | SU(2) \(\lambda = \#\mathcal{R}^*/2\) | Agreement |
|------------|------------|----------------------------------|----------------------------------------|-----------|
| Σ(2,3,5)   | −8         | 1                                | 1                                      | yes (rfl) |
| Σ(2,3,7)   | −8         | 1                                | 1                                      | yes (rfl) |
| Σ(2,3,11)  | −16        | 2                                | 2                                      | yes (rfl) |
| Σ(2,5,7)   | −16        | 2                                | 2                                      | yes (rfl) |

For Σ(3,4,5) and Σ(3,5,7) the bridge file records different numbers on the two sides (Milnor 2 vs SU(2) 1, and Milnor 4 vs SU(2) 2). Fintushel–Stern requires them to agree. Until the definitions or the counts are reconciled, those two rows are omitted.

**Formation reading:** Pairwise coprimality alone makes \(\Sigma(p,q,r)\) a homology 3-sphere and supplies an integer Seifert fibration realizing it. The singularity link is classified and fibered as a homology sphere; it is not turned into a topological sphere.

---

## 2. The 2-Adic Ring Relation and Spectral Circle Theorem

### The relation (plain)

On the finite ring \(\mathbb{Z}/2^n\mathbb{Z}\) (\(n \ge 1\)), every element \(x\) is sent to exactly two images under the directed relation:

\[
x \mapsto 3x \qquad\text{and}\qquad x \mapsto 3x - 1.
\]

The matrix \(D_n\) of this relation is 2-regular. In the character basis of the additive group (Pontryagin dual), the same matrix acts by a pure monomial shift:

\[
(D_n \chi_k)(x) = (1 + \omega_n^{-k})\,\chi_{3k}(x).
\]

### Why the eigenvalues lie on circles

Characters \(\chi_k\) with \(k\) of exact order \(2^m\) (\(1 \le m \le n\)) are permuted among themselves by \(k \mapsto 3k\).

- \(m = 1\): the weight \(1 + \omega^{-k} = 0\), contributing eigenvalue 0.
- \(m = 2\): one 2-cycle with weight product \(|1+i||1-i| = 2\), so the eigenvalues have modulus \(\sqrt{2}\).
- \(m \ge 3\): two cycles of length \(2^{m-2}\), swapped by complex conjugation. Since \(\Phi_{2^m}(-1) = 2\), each cycle’s weight product has modulus \(\sqrt{2}\).

A monomial cycle of length \(L\) with weight product \(W\) has eigenvalues equal to the \(L\)-th roots of \(W\). So for \(m \ge 2\), every eigenvalue from level \(m\) lies on the circle \(|\lambda| = 2^{2^{-(m-1)}}\). Together with 2 (from \(k = 0\)) and 0, this gives

\[
\mathrm{spec}(D_n) \subset \{0, 2\} \cup \bigcup_{m=2}^{n} \bigl\{ |\lambda| = 2^{2^{-(m-1)}} \bigr\},
\]

with finitely many eigenvalues on each circle. (A finite matrix cannot fill a continuum.)

### Continuous limit on \(\mathbb{Z}_2\)

The same relation defines the transfer operator

\[
(\mathcal{L} f)(x) = f(3x) + f(3x-1).
\]

Haar measure is expected to be the unique conformal Gibbs state, and correlations are expected to decay exponentially with Lyapunov exponent \(\frac12 \ln 2\). Neither claim is covered by the 0-sorry formalization; both are listed under open edges.

**Formation reading:** One affine relation on a 2-adic ring forces the eigenvalues of the finite matrix onto a discrete family of circles whose radii are determined by the multiplicative order of 3 modulo \(2^n\). The oscillation is rigidly quantized.

---

## 3. Lean Snippet (Bridge Core)

```lean
/-- Bridge Theorem: Unifies Brieskorn graph condition and Seifert homology-sphere solvability. -/
theorem brieskorn_seifert_bridge_3point (p q r : ℕ)
    (hpq : p.Coprime q) (hpr : p.Coprime r) (hqr : q.Coprime r) :
    Brieskorn.brieskornSphereCondition (Brieskorn.brieskornThreeExponents p q r) ∧
    ∃ (l0 : ℤ) (l : Fin 3 → ℤ), IsHomotopySphere (vec3 p q r) l0 l :=
  ⟨pairwiseCoprime_3_implies_brieskornSphere p q r hpq hpr hqr,
   pairwise_coprime_exists_sphere (pairwiseCoprime_vec3_of_coprime p q r hpq hpr hqr)⟩

/-- Explicit certificate for the Poincaré homology sphere Σ(2,3,5). -/
def bridge_2_3_5 : SeifertBrieskornBridge3 where
  p := 2
  q := 3
  r := 5
  l0 := 1
  l := twist3 1 1 1
  signature := -8
  casson_milnor := 1
  casson_su2 := 1
```

(The spectral-circle magnitude is formalized in the adelic-spectral-zeta Dynamics modules via the character-action lemma and the cyclic monomial eigenvalue formula.)

---

## 4. Combined Formation Sketch

| Layer              | Discrete Condition                              | Geometric / Spectral Outcome                                      | Resolution Mechanism                                      |
|--------------------|-------------------------------------------------|-------------------------------------------------------------------|-----------------------------------------------------------|
| Singularity link   | Pairwise coprimality of \(p,q,r\)               | Integral homology 3-sphere + Seifert fibration                    | Seifert order \(|O|=1\) classifies the link as a homology sphere |
| Spectral structure | Affine relation \(x \mapsto 3x, 3x-1\) on \(\mathbb{Z}/2^n\mathbb{Z}\) | Eigenvalues lie on circles of radius \(2^{2^{-(m-1)}}\)           | Multiplicative order forces pure circular eigenvalues     |
| Continuous limit   | Same relation on \(\mathbb{Z}_2\)               | Expected exponential mixing (not formalized)                      | Spectral gap expected to prevent unbounded growth         |

**Sketch hypothesis (open):**

A formation process that classifies singularity links as homology spheres while sustaining controlled oscillation can be assembled from two discrete mechanisms:

1. An arithmetic coprimality condition that produces a Brieskorn homology 3-sphere together with a Seifert fibration, converting a singularity link into a classified, fibered homology 3-manifold.
2. An affine dynamical relation on a 2-adic ring whose character action is monomial, forcing the eigenvalues of the finite matrix onto exact circles whose radii are fixed by the multiplicative order.

Together they give a candidate regularization pathway: the singularity link is algebraically classified as a homology sphere, while the spectral content of the dynamical relation remains confined to discrete circular orbits.

Open edges (not machine-checked in this note):
- Global adelic Euler-product models and GUE comparisons.
- Continuous heat-kernel asymptotics.
- Haar/Gibbs uniqueness and the Lyapunov exponent \(\frac12 \ln 2\) on \(\mathbb{Z}_2\).
- Reconciliation of the Milnor and SU(2) Casson values for Σ(3,4,5) and Σ(3,5,7).
- Any direct embedding of the 2-adic spectral circles into Seifert fibration data.
- The graph criterion contributes nothing topological in three variables.

---

## 5. Minimal Working References

- Bridge theorems and certificates: `sneed-and-feed/original-research` → `Formalization/GeneralSeifertClassification/BrieskornBridge.lean`
- Milnor signature and Casson: `Formalization/BrieskornManifolds/MilnorSignature.lean`
- SU(2) counts and equality theorems (four cases only): `Formalization/BrieskornSU2CharacterVariety/CassonInvariant.lean` and `RepresentationCounts.lean`
- Spectral Circle statement and character action: `sneed-and-feed/adelic-spectral-zeta` → papers/collatz_spectral_circle.md and formalization/Formalization/Dynamics/
- All discrete cores compile with 0 sorries under standard Lean 4 kernel axioms.

Ready for lattice, agent prompt, or further formalization.
