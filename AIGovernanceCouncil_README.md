# AIGovernanceCouncil

**An experimental governance simulation with inspectable belief dynamics, citizen profiles, geometric views, and Hodge network analysis.**

AIGovernanceCouncil explores how modeled people exchange ideas, develop competencies, form groups, and participate in explicit governance rules. Its default society contains **230 residents: 200 citizens, eight theory councillors, and 22 review councillors**. A SimCity-like island makes the population approachable; the underlying numerical model and its recorded evidence remain separate from that presentation.

This package contains the complete, unchanged **HyperCouncil v8.87.0** source, new mathematical documentation, and two standalone HTML explorers. AIGovernanceCouncil is the project's new name, not a newly assigned backend version. The project is a research laboratory: mathematical consistency does not establish that these synthetic coefficients accurately describe people or improve real governance.

## Start with the interactive explorers

Download and open either HTML file in a modern browser. Both include their data, styles, and JavaScript: no installation, server, internet connection, or API key is required.

| Explorer | What you can do |
|---|---|
| [Hodge Explorer](study/AIGovernanceCouncil_Hodge_Explorer.html) | Inspect gradient, curl, and harmonic flow; fill/unfill a triangle; vary a teaching flow; select the recorded 230-agent example; inspect numerical diagnostics; pause markers and reset the view. |
| [Island and Citizen Profiles](study/AIGovernanceCouncil_Island_and_Citizens.html) | Click, search, or select any resident; inspect six competencies, five belief memberships, and recorded history; focus, pan, zoom, and reset the island. The selected avatar stops walking. |

The profile composite is `100 × mean(six competency coordinates)`. Trait bars use consistent colors. Missing quality-of-life or aspiration data remain unavailable rather than being invented. The composite is a model summary, not an intelligence test, moral ranking, or voting qualification.

These files animate avatars and flow markers around a **fixed recorded interval**. Animation does not create new measurements. The Hodge teaching mode can vary a deliberately synthetic flow and is labeled accordingly. The island is illustrative geography, not the social-feature coordinates used to construct contacts. Live chat and voice belong to the full application; these offline explorers do not connect to those services.

## Short visual previews

The recordings demonstrate the explorers' controls and presentation. Each silent MP4 is 22 seconds at 1440×900 and 30 frames per second; the smaller GIF previews loop a shorter excerpt. Moving markers and avatars are display animation; the embedded recorded dataset remains fixed.

| Hodge network | Island and citizen profile |
|---|---|
| ![Hodge Explorer animated preview](media/Hodge_Explorer.gif) | ![Island and citizen profile animated preview](media/Island_and_Citizens.gif) |
| [Watch the MP4](media/Hodge_Explorer.mp4) | [Watch the MP4](media/Island_and_Citizens.mp4) |

## The mathematics in ten steps

The following is a source-based overview. [The complete derivations and geometry appendix](study/AIGovernanceCouncil_Derivations.md) provide proofs, conventions, implementation details, and limitations. Paths below refer to the extracted repository.

### 1. Describe each resident, then project for inspection

The main eight-dimensional descriptor is

$$
z_i=(p_i,o_i,2m_i-1,2a_i-1,2u_i-1,2r_i-1,x_{i1},x_{i2}),
\qquad u_i=1-\left(\frac{p_i-q}{2}\right)^2.
$$

Here $p_i$ is preferred policy, $o_i$ expressed opinion, $m_i$ reward memory, $a_i$ adoption, $r_i$ effective resistance, $q$ shared policy, and $x_i$ a two-dimensional social-feature position. All are dimensionless synthetic variables.

The crystal uses a common initial PCA origin and three axes:

$$y_i(t)=V_3[z_i(t)-\mu_0]/\rho.$$

A convex hull encloses these projected points. The fixed axes make recorded motion comparable; PCA can lose information, so a 3D separation is not a full-state distance. Mismatch-triggered turnover uses the original 8D centroids, not the rendered hull. Eight coefficients can be packaged as an octonion, but this descriptor/PCA pipeline does not thereby use octonion multiplication as a decision rule.

### 2. Specify the contact complex and its cutoff

Main geometric contacts use the social-feature positions:

$$\{i,j\}\in E\iff\|x_i-x_j\|\le\epsilon.$$

A **Rips** triangle exists when all three pairwise edges exist. A **Čech** triangle additionally requires a common intersection of balls of radius $\epsilon/2$. For an acute planar triangle, its minimum enclosing-circle radius is $abc/(4A)$; for a right/obtuse triangle it is half the longest side. The Čech test compares that radius with $\epsilon/2$.

The shared edge cutoff and different face rules matter: the same visible loop can be filled in one complex and unfilled in another. Committee contacts, the separate full-8D nearest-neighbor diagnostic, and avatar proximity are not interchangeable with this geometric complex.

### 3. Distinguish copied content, net flow, and gross exchange

Let $W_{ij}\ge0$ describe contacts, $d_i$ source routing propensity, and $\ell_j$ the recipient's belief-rate scale. The measured copying rule uses

$$
D=\max\left(1,\max_j\sum_iW_{ij}d_i\right),\quad
C_{ij}=\frac{W_{ij}d_i(1-e^{-\lambda\ell_j\Delta t})}{D},
$$

$$b'_j=\left(1-\sum_iC_{ij}\right)b_j+\sum_iC_{ij}b_i.$$

Because $0\le\sum_iC_{ij}\le1$, this is convex and preserves $[0,1]$ belief bounds. On an oriented edge,

$$F_{i\to j}=C_{ij}b_i,\quad f_{ij}=F_{i\to j}-F_{j\to i},\quad G_{ij}=F_{i\to j}+F_{j\to i}.$$

Zero net flow can coexist with substantial gross two-way exchange. These are copied endorsement scores, not conserved material. With incoming/outgoing coefficient sums $r^{\rm in},r^{\rm out}$,

$$\Delta b=B_1f+b\odot(r^{\rm out}-r^{\rm in}).$$

Therefore Hodge divergence alone need not equal the belief update; innovation, competency learning, and other declared channels add further changes.

### 4. Decompose observed flow with the Hodge Laplacian

Orient every edge from its lower to higher index. The vertex-edge boundary matrix $B_1$ assigns source $-1$ and target $+1$. The edge-triangle matrix $B_2$ uses

$$\partial[a,b,c]=[b,c]-[a,c]+[a,b],\qquad B_1B_2=0.$$

Sparse least squares gives

$$f=\underbrace{B_1^T\phi}_{g\;\text{gradient}}+
\underbrace{B_2\psi}_{c\;\text{curl}}+
\underbrace{h}_{\text{harmonic}},\qquad B_1h=0,\quad B_2^Th=0.$$

Gradient flow fits node-potential differences; curl combines filled-triangle circulations; harmonic flow detects circulation not removed by either operation. The unweighted edge operator is

$$L_1=B_1^TB_1+B_2B_2^T,\qquad
v^TL_1v=\|B_1v\|^2+\|B_2^Tv\|^2\ge0.$$

Orthogonality yields $\|f\|^2=\|g\|^2+\|c\|^2+\|h\|^2$. Displayed energy shares are squared signal-norm fractions, not joules or shares of truth. Real-valued Hodge nullity and separately computed finite-field homology require their own coefficient conventions.

**Tiny exact example:** for edges $(AB,AC,BC)$ and $f=(3,-2,1)$, the gradient is $(1,0,-1)$ and residual $(2,-2,2)$. Fill triangle $ABC$: the residual is curl. Leave it unfilled: the residual is harmonic. Squared norms are $14=2+12$. A spatial cycle does not prove periodic behavior over time.

### 5. Read the belief terrain as a density landscape

The belief-specific eight-vector contains five memberships, rescaled opinion, memory, and adoption. After its fixed two-axis projection, the terrain estimates

$$p_h(y)=\frac{1}{2\pi h^2\sum_iw_i}\sum_iw_i
 e^{-\|y-y_i\|^2/(2h^2)},\qquad U_h=-\log p_h.$$

Dense clusters become valleys. The optional contrast is $R=U_h-U_{3h}=\log(p_{3h}/p_h)$, while the displayed height is the monotone transform $Z=\operatorname{asinh}(v/6)/\operatorname{asinh}(5)$. Finer grids add no observations; changing bandwidth changes the estimator.

Candidate hyperedges are **connected components of size at least three under a full-8D RMS-distance threshold**. They are not computed energy barriers or necessarily cliques. A proposed barrier could use a minimum-over-paths maximum of $U_h$, but the terrain does not calculate or charge that cost to citizens.

A separate, explicitly prescribed OU probe evolves Gaussian components with

$$\mu_i(\tau)=\mu+e^{-\kappa\tau}(y_i-\mu),\quad
\Sigma(\tau)=[\sigma_{\rm eq}^2+(\sigma_0^2-\sigma_{\rm eq}^2)e^{-2\kappa\tau}]I.$$

It can show basins coalescing. Its $F=-T\log p$ is a modeled effective potential, not measured physical free energy or subsequent observed citizen motion.

### 6. Update competencies without inventing a psychometric score

Six domains describe analytical reasoning, learning adaptability, collaboration, systems foresight, creative synthesis, and metacognitive calibration. The programmed target is

$$T_i=\operatorname{clip}(0.48P_i+0.25\,\mathrm{peers}_i+0.20\,\mathrm{practice}_i+0.07m_i,0,1),$$

$$s'_i=(1-\alpha_i)s_i+\alpha_iT_i,\qquad\alpha_i=1-e^{-r_i^{\rm learn}\Delta t}.$$

$P_i$ denotes individual learning potential. This update preserves bounds and permits changing ranks as practice and exposure change. The profile's equal-weight composite is $100\sum_{k=1}^6s_{ik}/6$. Culture and appearance are not capability multipliers. A proposed civil-service examination is a future task-based assessment, not this composite renamed and not an implemented restriction on voting.

### 7. Keep fuzzy degrees and electoral preferences explicit

The five live memberships can be simultaneously high; they need not sum to one. Membership is a degree, not calibrated probability of truth. The live world's numerical voting rules, a separate Sugeno review-priority experiment, and a separate post-decision fuzzy-confidence diagnostic are distinct modules.

The democratic election utility is

$$U(v,c)=0.60\left[\sum_{k=1}^{4}w_{vk}P_{ck}+w_{v5}W_{vc}\right]+0.25A_{vc}+0.15R_c,$$

where normalized urgency weights meet candidate commitments, wants similarity, belief alignment, and reputation. Belief alignment is $A_{vc}=1-\frac15\sum_j|b_{vj}-b_{cj}|$. All current identities can vote and stand with equal voting weight. Upper seats are selected first, then review seats from remaining candidates, with deterministic tie-breaking and replayable receipts. The coefficients are transparent design choices, not a proof of legitimacy.

### 8. Interpret the 8×8 response matrix correctly

The application fits recent identity-consistent transitions using ridge regression:

$$J=[(X_c^TX_c+\lambda I)^{-1}X_c^TY_c]^T,\qquad G=J^TJ.$$

Thus $v^TGv=\|Jv\|^2\ge0$ and $\lambda_i(G)=\sigma_i(J)^2$. This is a **fitted one-step affine response**, not automatic differentiation of the entire simulator. It measures gains per recorded step and depends on descriptor scaling. Observed/hidden four-coordinate maps are embedded in eight labels with inactive zeros; their rank is at most four.

### 9. Separate curvature, bivectors, and Hopf geometry

The conditional four-dimensional curvature metric is separately declared:

$$g_{ab}(x)=\delta_{ab}+\frac{s}{M}\sum_e
 e^{-\|x-m_e\|^2/(2h^2)}u_{e,a}u_{e,b},\qquad I\preceq g\preceq(1+s)I.$$

Analytic metric derivatives produce Levi-Civita, Riemann, Ricci, and Weyl tensors. The orthonormal bivector basis contains six oriented planes: $12,13,14,23,24,34$. Hence the Weyl operator is **6×6**, distinct from the 8×8 response matrix. Separate ellipsoid probes prescribe an induced geometry; round spheres have zero Weyl curvature, and the seven-dimensional sphere has 21 bivector directions.

For normalized quaternion pairs, $H(a,b)=(2a\bar b,|a|^2-|b|^2)$ is the quaternionic Hopf map $S^7\to S^4$, not the octonionic $S^{15}\to S^8$ map. These diagnostics are not spacetime curvature, Einstein dynamics, or experimentally established social physics.

### 10. Separate bounded integration from smooth presentation

Memory, opinion, and competency updates use frozen-target exponential relaxation. Policy uses clipped Euler feedback:

$$q'=\operatorname{clip}\left(q+\Delta t\,k\gamma\eta\frac{\tau-q}{2},-1,1\right).$$

The allowed timestep and gain keep this policy step convex for a frozen target; this does not prove global stability of the coupled stochastic society. Adoption uses a bounded frozen-hazard approximation, so timestep-refinement checks still matter.

SLERP smoothly interpolates unit orientations. It neither solves belief dynamics nor adds measurements. Model time, session scheduling, and browser animation have different meanings. A 55–60-fps rendering target is device dependent and does not imply that many independently simulated states per second.

## Source map

| Method | Main implementation in `app/` |
|---|---|
| State, contact complexes, policy integration | `council_network.py` |
| Eight descriptors, mismatch, crystal projection | `council_turnover.py`, `council_crystal.py` |
| Measured copying and Hodge decomposition | `council_nuance.py`, `council_hodge_network.py` |
| Belief terrain and separate OU probe | `council_belief_view.py`, `belief_landscape_ui.js` |
| Competencies, fuzzy explanations, elections | `council_civilization.py`, `council_fuzzy_explain.py`, `council_elections.py` |
| Fitted response and geometry | `council_latent_linear.py`, `council_curvature.py`, `council_geometry.py`, `council_sphere_curvature.py` |

## Fixture provenance and executed verification

The examples were newly generated by the unmodified application with **seed 17, eight steps, and Δt = 0.1**. They are reproducible synthetic fixtures, not a recovered saved session. The island shows arrival step **8**; Hodge exchange belongs to the incoming interval **7 → 8**, using source identities and incoming topology. The full complex has **230 vertices, 1,956 edges, and 8,219 triangles**.

| Check | Recorded outcome |
|---|---|
| Source integrity | All 1,364 original manifest-listed files match size/SHA-256 |
| Native Chromium browser checks | 78/78 passed; zero uncaught errors and external network requests |
| Geometry checks | 20/20 source-specific algebra and derivative checks |
| Hodge cases | 15 production-backed cases compared with independent dense and browser solvers |
| Full incoming complex | Boundary-of-boundary zero; converged sparse decomposition; reconstruction error zero |
| Exact triangle arithmetic | Independently confirmed with Wolfram Language |

See [validation scope](study/VALIDATION_NEW_ARTIFACTS.md), [snapshot receipt](study/snapshot_verification.json), [browser receipt](study/QA_HTML.json), and [Wolfram result](study/WOLFRAM_CHECK.md). Native Hodge JSON omits triangles: the explorer does not pretend to reverify $B_2$ for that import. `hodge_exact_complex.json` preserves the complex for Python checks. The receipts cover the supplied files, not future modifications or paid voice services; the full legacy application suite was not rerun for this companion package.

## Reproduce or run

Python examples require NumPy and SciPy, without an AI service. In a dedicated Python environment, install them with `python -m pip install numpy scipy`. The exact environment used for the supplied fixtures is recorded in `study/snapshot_verification.json`. From the repository root:

```bash
python study/generate_snapshots.py --app app --output-dir study
python study/embed_snapshots.py --directory study
python study/generate_hodge_reference_cases.py --app app --output study/hodge_reference_cases.json
python study/verification_geometry.py --app app --output study/GEOMETRY_QA.json
node study/test_hodge_explorer.cjs
```

For browser tests, install Playwright and Chromium in the development environment, then run `node study/qa_html.cjs`. An external browser path can be supplied through `AIGC_CHROMIUM_EXECUTABLE`. Regeneration changes timestamps/timings and can introduce platform-level floating-point differences; rerun checks after changing fixtures.

For the original dashboard, use Python 3.11 or later and follow [RUNNING.md](RUNNING.md):

```bash
python launch.py
```

The launcher prepares dependencies. Offline simulation needs no API key; optional connected conversation requires configured credentials. The new explorers do not patch or claim to resolve existing proxy, microphone, authentication, or server issues.

## Repository contents and research scope

`app/` holds the unchanged source and tests; `MANIFEST.json` the original manifest; `launch.py` and `RUNNING.md` the launcher instructions. `SOURCE_README.md` and `VALIDATION.md` preserve original release context. `study/` contains the new guide, explorers, fixtures, scripts, and receipts. `media/` contains previews. Earlier publication notes remain under `docs/publication/`.

The current work implements transparent numerical rules and diagnostics. Real-world calibration, comparative governance efficacy, a validated civil-service examination, and interpretation of density barriers as actual costs remain research questions. Citizen conversations can use recorded context, but generated speech is not a new measurement. This is a GitHub-ready folder, not an already published repository; no license is assigned on your behalf.

## Primary references

- Lim (2020), **Hodge Laplacians on Graphs**: [manuscript](https://arxiv.org/abs/1507.05379), [DOI](https://doi.org/10.1137/18M1223101).
- Jiang, Lim, Yao & Ye (2011), **Statistical Ranking and Combinatorial Hodge Theory**: [manuscript](https://arxiv.org/abs/0811.1067), [DOI](https://doi.org/10.1007/s10107-010-0419-x).
- GUDHI Project: [Rips complex](https://gudhi.inria.fr/python/latest/rips_complex_user.html) and [Čech complex](https://gudhi.inria.fr/doc/latest/group__cech__complex.html) documentation.
- Zadeh (1965), **Fuzzy Sets**: [DOI](https://doi.org/10.1016/S0019-9958(65)90241-X).
- Baez (2002), **The Octonions**: [manuscript](https://arxiv.org/abs/math/0105155).

These establish mathematical background, not empirical validation of this application's governance assumptions.
