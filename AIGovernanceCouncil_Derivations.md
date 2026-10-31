# AIGovernanceCouncil: mathematics, model derivations, and physical interpretation

**Source baseline:** the supplied HyperCouncil v8.87 application, renamed here AIGovernanceCouncil. This guide describes equations implemented in that source. It is not a claim that every dashboard label, optional experiment, or future feature shares one mathematical model. Paths below are relative to the application source directory, `app/`.

**What this project is:** a synthetic, agent-based governance laboratory with bounded numerical states, explicit policy rules, interaction networks, topological diagnostics, and optional language-model conversation. Its mathematical constructions are meaningful, but its social coefficients are modeling choices. This is a derivation of the implemented model, not a derivation of society from fundamental physics or evidence of improved real-world governance.

## 1. Start with the state, units, and clocks

The default main-world population is 200 citizens plus 30 officials. Configurations can change those counts. At a recorded model time, person or role slot $i$ has:

| Symbol | Meaning | Range / units |
|---|---|---|
| $p_i,o_i$ | Preferred policy and expressed opinion | $[-1,1]$, dimensionless |
| $m_i,a_i$ | Reward memory and adoption level | $[0,1]$, dimensionless |
| $x_i$ | Two-dimensional social-feature position | Bounded synthetic coordinates; not island geography |
| $b_i$ | Five independent belief memberships | $[0,1]^5$, not a probability simplex |
| $s_i$ | Six practiced competency coordinates | $[0,1]^6$, not measured intelligence |
| $q$ | Shared scalar policy | $[-1,1]$ |
| $\Delta t$ | Numerical model interval | Dimensionless model time; default $0.1$ |

`council_network.py:_make_agents` initializes seeded synthetic states. Identity-owned attributes follow people when office occupants change; transport endpoints use the current role slots. That distinction matters when interpreting a time series across an election or replacement.

There are three clocks: model time, active-session scheduling time, and browser animation time. A 60-fps rendering interpolates a recorded interval; it does not supply 60 independent social observations per second. Election scheduling and camera animation must not silently redefine $\Delta t$.

Utility is explicitly prescribed:

$$u_i(q)=1-\left(\frac{p_i-q}{2}\right)^2.$$

Since $|p_i-q|\le2$, $0\le u_i\le1$. “Reward,” “belief energy,” and illustrative currency in this application are not automatically joules, bits, or real money. A physical unit requires an independently specified measurement and conversion.

## 2. Eight-dimensional descriptors and the latent crystal

`council_turnover.py:shared_latent` constructs the main eight-vector

$$z_i=(p_i,o_i,2m_i-1,2a_i-1,2u_i-1,2r_i-1,x_{i1},x_{i2}),$$

where $r_i=\operatorname{clip}(r_0+0.45(1-m_i),0,0.98)$ is effective resistance. The eight coordinates are declared features, not discovered physical dimensions.

To render a crystal, `council_crystal.py:_fixed_projection` first centers the initial combined population: $Z_c=Z_0-\mathbf1\mu_0^T$. It diagonalizes $Z_c^TZ_c$, selects its three largest-eigenvalue eigenvectors, and fixes their signs reproducibly. With these eigenvectors as the rows of $V_3$,

$$y_i(t)=\frac{V_3[z_i(t)-\mu_0]}{\rho}.$$

The common origin and axes remain fixed across roles and frames; $\rho$ is a recorded display scale. This prevents independently refitted cameras from manufacturing apparent group motion. A convex hull wraps the projected points, preserving their actual degeneracy rather than inventing a third dimension. PCA can lose information: report explained variance and do not treat projected distance as full-state distance. Display scaling may inspect a completed trajectory; this is not a predictive input.

The mismatch gate uses the **full** eight-dimensional centroids:

$$d=\frac{\|\bar z_{\mathrm{citizens}}-\bar z_{\mathrm{officials}}\|_2}{2\sqrt8}.$$

`latent_comparison` and `TurnoverGate.advance` combine this gap with a dissatisfaction quorum, dwell time, hysteresis, and cooldown. Projection does not trigger turnover. Thus some geometry is operational, while the three-dimensional hull itself is a view.

Do not combine these separate constructions:

| Construction | Input and purpose |
|---|---|
| Main latent crystal | The eight features above, fixed initial 3D PCA |
| Belief latent frame | Five memberships, rescaled opinion, memory, adoption; `council_belief_view.py:belief_latent_frame` |
| Latent neighbor diagnostic | Symmetric union of three nearest neighbors in full 8D; `council_latent_connectivity.py:_neighbours` |
| Main contact complex | Two-dimensional $x_i$, Rips or Čech; used below |
| Avatar island | Navigation/display space, not the contact metric |

An eight-vector can be written as octonion coefficients $z=\sum_{k=0}^7z_ke_k$. That labeling alone does not make PCA, transport, or decisions use octonion multiplication. Claims about nonassociativity require an actual product in the governing equations; the main crystal construction does not supply one.

## 3. From proximity to a Rips or Čech 2-skeleton

`council_network.py:build_complex` uses distances in the **2D social-feature positions**:

$$\{i,j\}\in E\iff\|x_i-x_j\|\le\epsilon.$$

Both options share these edges. A Rips triangle is present when all three pairwise edges are present. A Čech triangle requires a common intersection of three closed balls of radius $\epsilon/2$. This radius convention avoids a frequent factor-of-two error [3,4].

For three planar points, `_cech_mask` tests their minimum enclosing circle. If the triangle is right, obtuse, or degenerate, its radius is half the longest side. For an acute triangle with side lengths $a,b,c$ and area $A$,

$$R=\frac{abc}{4A};\qquad \text{include the triangle iff }R\le\epsilon/2.$$

For example, an equilateral triangle with side $\epsilon$ is a Rips triangle, but its enclosing radius $\epsilon/\sqrt3$ exceeds $\epsilon/2$, so it is not a Čech triangle at that cutoff.

Only the 2-skeleton is needed for the edge Hodge operator: vertices, edges, and filled triangles. Higher simplices are unnecessary for $H_1$. Additional committee contacts and nonlocal copying events are not automatically geometric triangles. The Hodge observatory separates them rather than filling every apparent loop.

The complex changes when coordinates or the cutoff change. Changing a camera cannot change this topology. `build_complex` computes connected components and, within explicit limits, first homology over $\mathbb F_2$ by boundary reduction. It returns “not computed” beyond those limits; absence of a reported value is not zero topology.

## 4. Derive the measured exchange before decomposing it

In `council_nuance.py:NuanceEngine._transport`, let $W_{ij}\ge0$ encode allowed contacts, including declared role scaling and optional committee or triangle amplification. Let $d_i\ge0$ be the source's routing propensity. Define

$$D=\max\left(1,\max_j\sum_iW_{ij}d_i\right),\quad
\alpha_j=1-e^{-\lambda\ell_j\Delta t},\quad
C_{ij}=\frac{W_{ij}d_i\alpha_j}{D},$$

where $\ell_j$ is the recipient's role-specific belief-rate scale. The code's $C_{ij}$ means a copying coefficient **from source $i$ to recipient $j$**. For each belief coordinate,

$$b'_j=\left(1-\sum_iC_{ij}\right)b_j+\sum_iC_{ij}b_i.$$

Because $0\le\sum_iC_{ij}\le\alpha_j\le1$, this is a convex combination. If all old values lie in $[0,1]$, all new values remain there. This bound is a mathematical consequence of normalization; it does not establish convergence of the entire changing network.

On the canonical edge $i<j$, the recorded directed copied contents are

$$F_{i\to j}=C_{ij}b_i,\quad F_{j\to i}=C_{ji}b_j,\quad
f_{ij}=F_{i\to j}-F_{j\to i}.$$

The record also stores $r_{ij}=\min(F_{i\to j},F_{j\to i})$. Thus both directions can be recovered:

$$F_{i\to j}=r_{ij}+\max(f_{ij},0),\quad
F_{j\to i}=r_{ij}+\max(-f_{ij},0).$$

Gross exchange is $2r_{ij}+|f_{ij}|$. Consequently, **zero net flow can coexist with substantial two-way exchange**. An arrow depicts the net balance, not the absence or presence of conversation. Aggregate flow is the signed arithmetic mean over the five memberships; opposing belief-specific flows can cancel.

These are copied endorsement scores, not conserved material. With incoming coefficient $r_j^{\rm in}=\sum_iC_{ij}$ and outgoing coefficient $r_j^{\rm out}=\sum_kC_{jk}$, the pure copying increment satisfies

$$\Delta b=B_1f+b\odot(r^{\rm out}-r^{\rm in}).$$

Thus $B_1f$ alone need not equal belief change. Separate competency learning, innovation, nonlocal events, and civic feedback add further changes. Hodge arrows measure the recorded copying channel, not every cause of learning. Contents per interval can be divided by the recorded $\Delta t$ to obtain a rate per model-time unit.

For a concrete asymmetric example, let two memberships be $b=(0.2,0.8)$, with $C_{12}=0.1$ and $C_{21}=0.2$. The new values are $(0.32,0.74)$: their sum rises from $1$ to $1.06$. Forward and reverse copied contents are $0.02$ and $0.16$, net $-0.14$, gross $0.18$. Nothing violates the convex bound; copying is not a conserved transfer of a substance. Under the narrower uniform reciprocal pairwise rule, $C_{ij}=cA_{ij}$ and $f_{ij}=c(b_i-b_j)$ is a pure unweighted gradient. Heterogeneous weights or routing can change that decomposition without any extra circulation being added to the display.

## 5. Construct the Hodge operators, then prove the decomposition

The current implementation is `council_hodge_network.py`, not the separate benchmark helper in `council_core.py` [1,2]. Let $n,m,t$ be the counts of vertices, edges, and filled triangles. Orient edge $(i,j)$ from lower index to higher index. Define

$$B_1\in\mathbb R^{n\times m},\qquad (B_1)_{i,e}=-1,\quad(B_1)_{j,e}=+1.$$

Its column describes the boundary “target minus source.” For $a<b<c$, the triangle boundary is

$$\partial[a,b,c]=[b,c]-[a,c]+[a,b].$$

Putting these columns in $B_2\in\mathbb R^{m\times t}$ gives $B_1B_2=0$: the boundary of a boundary vanishes. This identity is checked numerically, not merely assumed from the plot.

For observed edge flow $f\in\mathbb R^m$, solve

$$\phi=\arg\min_v\|f-B_1^Tv\|^2,\qquad g=B_1^T\phi,$$

$$\psi=\arg\min_w\|f-g-B_2w\|^2,\qquad c=B_2\psi,\qquad h=f-g-c.$$

The code uses sparse LSMR with declared tolerances. Potentials may be nonunique—for example, adding a constant on a connected component does not change $g$—but the exact orthogonal projected edge flows are unique. In pseudoinverse notation,

$$P_g=B_1^T(B_1B_1^T)^\dagger B_1,\quad
P_c=B_2(B_2^TB_2)^\dagger B_2^T,\quad P_h=I-P_g-P_c.$$

The chain identity makes the gradient and curl spaces orthogonal. Least-squares residual orthogonality then gives

$$f=g+c+h,\qquad B_1h=0,\qquad B_2^Th=0.$$

Interpret the pieces precisely:

- **Gradient $g$:** differences of a fitted node potential. This is a global least-squares explanation of net exchange; it is not automatically ideological improvement or moral progress.
- **Curl $c$:** combinations of the boundaries of filled triangles. It captures local circulation relative to the chosen 2-complex.
- **Harmonic $h$:** circulation with neither divergence nor triangular curl. It represents nontrivial first-homology directions in that complex. A genuine zero is allowed.

The edge Laplacian is

$$L_1=B_1^TB_1+B_2B_2^T.$$

For any $v$, $v^TL_1v=\|B_1v\|^2+\|B_2^Tv\|^2\ge0$, proving symmetry and positive semidefiniteness. Hence $\ker L_1=\ker B_1\cap\ker B_2^T$, and

$$\dim\ker L_1=\beta_1(K;\mathbb R)=m-n+\beta_0-\operatorname{rank}_{\mathbb R}B_2.$$

The independently reported topology reduction uses $\mathbb F_2$, whereas the Hodge operator uses real coefficients. Do not equate their Betti numbers without checking the coefficient-field issue for the complex; numerical spectral nullity also has a tolerance.

Orthogonality proves

$$\|f\|^2=\|g\|^2+\|c\|^2+\|h\|^2.$$

The displayed “energy fractions” divide these squared norms by $\|f\|^2$. They are Euclidean signal-energy shares, not thermodynamic energy or fractions of truth. For zero total flow, the code reports zero shares. Transport weights affect measured $f$, but this implementation's inner product and boundary matrices are unweighted.

The observatory checks reconstruction, chain identity, harmonic divergence/curl, orthogonality, and solver convergence. It retains all admitted simplices up to explicit resource limits: 300 vertices, 44,850 edges, 200,000 triangles. Full eigenvalue spectra are computed only for at most 384 edges; larger valid cases still receive the sparse decomposition. A cohort subcomplex omits cross-boundary edges and reports their exchange separately, so its harmonic structure can differ from the whole population's.

## 6. An exact example: the same loop can be curl or harmonic

Take vertices $A,B,C$, edges ordered $(AB,AC,BC)$, and

$$B_1=\begin{pmatrix}-1&-1&0\\1&0&-1\\0&1&1\end{pmatrix},\qquad
f=\begin{pmatrix}3\\-2\\1\end{pmatrix}.$$

One least-squares potential is $\phi=(-1/3,2/3,-1/3)^T$. Therefore

$$g=(1,0,-1)^T,\qquad f-g=(2,-2,2)^T.$$

If the triangle is filled, $B_2=(1,-1,1)^T$ and $c=2B_2$, $h=0$. If it is unfilled, $B_2$ has no columns, $c=0$, and $h=(2,-2,2)^T$. In both cases the reconstruction is exact. Squared norms are $14=2+12$; gradient share is $1/7$, and the remaining $6/7$ changes interpretation with the complex.

This is why “I see a loop” is insufficient: one must know which faces are filled. The example is also a compact regression test for orientation, projections, and zero/nonzero harmonic behavior.

## 7. What physics analogies do—and do not—justify

The graph Laplacian $L_0=B_1B_1^T$ resembles a resistor-network or diffusion operator. Under an explicitly chosen idealized dynamics $\dot v=-L_1v$,

$$\frac{d}{dt}\frac12\|v\|^2=-v^TL_1v\le0.$$

Diagonalizing $L_1$ yields $v(t)=\sum_k e^{-\lambda_kt}\langle u_k,v(0)\rangle u_k$. Positive eigenmodes decay and zero modes remain constant. **This equation does not produce sustained oscillation.** A topological loop is a spatial property; a periodic pattern is a time-dependent property requiring time-series evidence or a suitable dynamical mechanism. The main observatory decomposes measured flows; it does not claim that their evolution obeys this diffusion equation.

If a hypothetical explicit-Euler diffusion integrator used $v_{k+1}=(I-\Delta tL_1)v_k$, spectral stability would require $\Delta t\lambda_{\max}\le2$. That bound belongs to that hypothetical integrator, not an undocumented simulation equation.

### The implemented belief terrain: a density-derived landscape

The terrain does have a defined scalar field. `council_belief_view.py:belief_latent_frame` supplies $z_i^b=(b_{i0},\ldots,b_{i4},(o_i+1)/2,m_i,a_i)$. Its fixed initial PCA is reused; `belief_landscape_ui.js:project` takes the first two axes to obtain $y_i\in\mathbb R^2$. At a recorded endpoint, `density`/`kdeValue` evaluate the normalized Gaussian kernel density estimate

$$p_h(y)=\frac{1}{2\pi h^2\sum_iw_i}\sum_iw_i
\exp\left[-\frac{\|y-y_i\|^2}{2h^2}\right],\qquad U_h(y)=-\log p_h(y).$$

Endpoint weights are $w_i=1$. The implementation uses separable Gaussian sums and a log-sum-exp fallback, with no artificial density floor. Peaks of estimated density become valleys of $U_h$: several clusters can produce several basins. They are distributional features in this projection, not necessarily distinct semantic beliefs. A point is one recorded agent; a minimum is not an agent. Sampling more grid cells improves the picture without adding observations, and changing bandwidth changes the estimator. A density can exceed one, so negative $U_h$ is legitimate. Values depend on the chosen coordinates and scale; they are not probabilities of ideological correctness.

`contrastGrid` separately computes

$$R(y)=U_h(y)-U_{3h}(y)=\log\frac{p_{3h}(y)}{p_h(y)}.$$

This signed local-density contrast can reveal small-scale structure against a broader background. Its minima need not be the raw field's minima. `displayHeight` maps either selected scalar $v=U_h$ or $R$ to

$$Z(v)=\frac{\operatorname{asinh}(v/6)}{\operatorname{asinh}(5)}.$$

This strictly increasing, unsaturated transform preserves extrema of the selected field; gain, centering, lighting, and camera motion remain display operations. An optional upper-tail cutaway omits raw-$U$ cells above a threshold rather than replacing them with a plateau. During playback, `cohortTransition` interpolates shared identities' own projected positions and fades entering/departing identities with weights $\alpha$ and $1-\alpha$. The weighted density between endpoints is a display construction; diagnostics retain recorded endpoints.

### Candidate groups are not energy barriers

The terrain's `graph` uses the original eight belief-state coordinates, independently of the 2D density:

$$\{i,j\}\in E_b\iff\sqrt{\frac18\sum_{k=1}^8(z^b_{ik}-z^b_{jk})^2}\le\epsilon_b.$$

Connected components with at least three identities are labeled **candidate hyperedges**. They need not be cliques: a chain can connect members farther apart than $\epsilon_b$. Their graph cycle rank is $|E_b|-|V|+\beta_0$, not the first Betti number of a triangle-filled complex. These candidates are read-only diagnostics, not newly inserted governing committees or measured energy barriers. `events` compares identity overlap to describe component mergers/splits in inspected adjacent frames.

The proposed “hyperbreak” interpretation therefore needs an extra modeling step. A density barrier from a basin minimum $a$ to $b$ could be defined as

$$\Delta U_{a\to b}=\inf_{\gamma:a\leadsto b}\max_{s\in[0,1]}U_h(\gamma(s))-U_h(a).$$

At a controlling saddle $s_*$, the difference is $\log[p_h(a)/p_h(s_*)]$: a dimensionless log-density contrast, expressible in bits after division by $\log2$. This terrain pipeline does not solve that minimax problem, charge agents that amount, or drive their beliefs by $-\nabla U_h$. An information-shaped barrier is a possible explicit extension; it is not an implemented energetic cost of persuasion. Projected basins, full-8D components, and transport hyperedges should remain distinct.

### The separate OU probe: prescribed smooth basin evolution

`ouState` implements an optional isotropic Ornstein–Uhlenbeck model seeded once from recorded projected points, with frozen mean $\mu$. It declares

$$dX_\tau=-\kappa(X_\tau-\mu)d\tau+\sqrt{2D}\,dW_\tau,
\qquad D=\kappa\sigma_{\rm eq}^2.$$

An integrating factor gives $X_\tau-\mu=e^{-\kappa\tau}(X_0-\mu)+\sqrt{2D}\int_0^\tau e^{-\kappa(\tau-s)}dW_s$. Thus each initial Gaussian component with center $y_i$ and covariance $\sigma_0^2I$ evolves exactly to

$$\mu_i(\tau)=\mu+e^{-\kappa\tau}(y_i-\mu),\qquad
\Sigma(\tau)=\left[\sigma_{\rm eq}^2+(\sigma_0^2-\sigma_{\rm eq}^2)e^{-2\kappa\tau}\right]I.$$

The mixture weights remain equal. `ouGrid` evaluates this mixture and $F_\tau=-T\log p_\tau$, then displays

$$Z_{\rm OU}=\frac{\operatorname{asinh}((F_\tau-F_{\rm ref})/(6T))}{\operatorname{asinh}(5)},
\qquad F_{\rm ref}=\min_{\text{current lattice}}F_\tau.$$

The sampled reference shifts heights, not potential differences; different export resolutions can have different sampled references. The harmonic **force potential** $V=\kappa\|y-\mu\|^2/2$ is distinct from the time-dependent density-derived $F_\tau$. At equilibrium, $p_\infty\propto e^{-V/D}$ and $F_\infty=(T/D)V+\text{constant}$; this does not identify $T$ with a physical belief temperature.

Centers converge and component widths relax, so initially separate basins may merge; a stationary final field is expected. This is analytic Gaussian-density evolution, not sampled Brownian trajectories or subsequent observed citizen motion. Subsequent governance records do not feed the seeded probe unless it is explicitly reseeded. Defaults $\kappa=0.08$, $\sigma_0=0.04$, $\sigma_{\rm eq}=0.16$, $T=1$ are modeling parameters. Smooth animation visualizes this prescribed process; it does not validate it as a law of belief formation.

## 8. Policy, memory, adoption, and numerical stability

`council_network.py:_sense`, `_review`, `_propose`, and `_apply` implement bounded feedback. Differentiating the declared utility gives

$$\frac{\partial u_i}{\partial q}=\frac{p_i-q}{2}.$$

The running experiment replaces the pure citizen gradient by $(\tau-q)/2$, where

$$\tau=(1-\omega)\,\operatorname{mean}_{\rm citizens}p_i+
\omega\,\operatorname{mean}_{\rm officials}o_i.$$

This is the gradient of a declared weighted quadratic objective using citizen preferences and officials' current expressed opinions—not proof of a socially optimal objective. With review multiplier $\gamma=1/(1+\mathrm{risk})$ and reward multiplier $\eta=0.5+0.5\operatorname{clip}(R_{\rm previous},0,1)$,

$$q'=\operatorname{clip}\left(q+\Delta t\,k\gamma\eta\frac{\tau-q}{2},-1,1\right).$$

For a frozen $\tau$, this is a convex relaxation with coefficient $\Delta t\,k\gamma\eta/2$. Allowed $\Delta t\le0.25$, $k\le2$, and multipliers at most one give a coefficient at most $0.25$. This proves a local bound, not universal convergence under elections, moving targets, stochastic changes, or additional civic feedback.

Memory uses frozen-target exponential relaxation:

$$m'=e^{-\lambda_m\Delta t}m+(1-e^{-\lambda_m\Delta t})u.$$

Opinion uses the same form with rate $1-r_i$ and target

$$\widehat o_i=(1-w_i)(Po)_i+w_iq,$$

where $P$ is row-stochastic and $w_i=0.2$ for citizens, $0.55$ for officials. Nonnegative contact weights plus a unit self-loop are row-normalized in `transport_weights`/`transition_matrix`. Thus $Po$ remains in the old opinion range. The separate row-vector walk updates by

$$\pi'=\pi[(1-\Delta t)I+\Delta tP].$$

For $0\le\Delta t\le1$, this preserves probability mass and nonnegativity. It is a different process from copied belief endorsement.

For adoption, `group_pressure` averages other-member adoption squared in committees, or products of the other two triangle members in simplicial mode. With nonnegative combined hazard $\lambda_i$, `spread_step` computes

$$a'_i=a_ie^{-\gamma\Delta t}+(1-a_i)(1-e^{-\lambda_i\Delta t}).$$

Both coefficients lie in $[0,1]$, so the result is bounded. This frozen-hazard first-order map omits within-step recovery/reinfection; it is not the exact solution of the coupled network process. Poisson renewal probabilities may be exact for a frozen rate while the full split update remains approximate. Bound preservation, timestep convergence, accurate event timing, and real-world validity are four different requirements.

## 9. Fuzzy memberships, competency profiles, and elections

The five live-world memberships concern institutional trust, solidarity/redistribution, autonomy/rights, spiritual/religious importance, and Stoic practice. Several can be high simultaneously. They need not sum to one; a degree of membership is not a calibrated probability that a proposition is true [5]. Group emergence uses combinations and clustering on these fixed axes, not automatic invention of entirely new semantic dimensions.

`council_fuzzy_explain.py:MECHANISMS` distinguishes three modules. The live world uses explicit votes and numerical feedback from memberships. A separate population experiment uses a Sugeno-style review priority. A separate council benchmark computes a post-decision confidence diagnostic. These must not be advertised as one universal fuzzy voting system.

For the population experiment, uncertainty $u$, novelty $n$, and missing provenance $m$ define

$$\mu_H=\max(u,n,m),\quad\mu_L=\min(1-u,1-n,1-m),\quad
\mu_M=0.4\max(0,1-|2u-1|),$$

$$\mathrm{priority}=\frac{0.9\mu_H+0.1\mu_L+0.5\mu_M}{\mu_H+\mu_L+\mu_M+10^{-12}}.$$

This allocates review need; it is not truth or competence. Only the separate experiment's `fuzzy_weighted` mode changes aggregation weights. Benchmark `fuzzy_confidence` is computed after selection and does not change its winner.

The citizen profile has six competency coordinates: analytical reasoning, learning adaptability, collaboration, systems foresight, creative synthesis, and metacognitive calibration. `council_civilization.py:CivilizationEngine.advance` builds a normalized neighbor-contact matrix $P_c$, and updates

$$T_i=\operatorname{clip}(0.48\,\mathrm{potential}_i+0.25(P_cs)_i+
0.20\,\mathrm{practice}_i+0.07m_i,0,1),$$

$$s'_i=(1-\alpha_i)s_i+\alpha_iT_i,\qquad\alpha_i=1-e^{-r_i^{\rm learn}\Delta t}.$$

This is bound-preserving for nonnegative rates. Practice can renew and targets move, allowing rank changes without implying a psychometric assessment. Initial role-conditioned skill priors overlap; appearance and cultural labels do not determine learning potential. A displayed percentage is $100s_{ik}$, and an equal-weight composite is $100\sum_ks_{ik}/6$. Neither is an eligibility test or a person's worth. Competencies affect a separate, earlier bounded peer-belief-learning channel; its effects are not fully attributed to Hodge arrows.

The democratic election module is `council_elections.py`, separate from merit-based succession. With normalized personal urgency weights $w_{vk}$, candidate commitments $P_{ck}$, wants similarity $W_{vc}$, belief similarity $A_{vc}$, and public reputation $R_c$,

$$U(v,c)=0.60\left[\sum_{k=1}^{4}w_{vk}P_{ck}+w_{v5}W_{vc}\right]
+0.25A_{vc}+0.15R_c,$$

$$A_{vc}=1-\frac15\sum_{j=1}^{5}|b_{vj}-b_{cj}|.$$

All inputs are bounded, so $U\in[0,1]$. Every current identity can vote and stand with equal voting weight. Voters approve their highest-utility candidates for upper seats first, then review seats among remaining candidates. Ties use approval counts, summed utility, and stable identity. Receipts retain the inputs. These coefficients and procedures are transparent policy choices, not a theorem of legitimacy or an optimal electoral design. The separate succession score is not a civil-service examination; such an examination would require a new declared task and validation model.

## 10. Quaternion geometry, fitted dynamics, and curvature

### A quaternionic Hopf map—not an octonionic one

`council_geometry.py:hopf_s4` normalizes a nonzero eight-vector once and splits it into Hamilton quaternions $(a,b)$ with $|a|^2+|b|^2=1$. It computes

$$H(a,b)=(2a\bar b,|a|^2-|b|^2)\in\mathbb R^5.$$

Quaternion norm multiplicativity gives

$$|2a\bar b|^2+(|a|^2-|b|^2)^2=(|a|^2+|b|^2)^2=1,$$

so the image lies on $S^4$. For any unit quaternion $r$, $H(ar,br)=H(a,b)$ because $(ar)\overline{br}=ar\bar r\bar b=a\bar b$. The fibers are $S^3$: this is $S^7\to S^4$, not the octonionic Hopf fibration $S^{15}\to S^8$ [6]. Normalizing the two blocks independently would erase their relative amplitude and change the map. A zero input has no such image.

SLERP interpolates orientations. For unit quaternions with angle $\theta$,

$$\operatorname{slerp}(q_0,q_1,u)=
\frac{\sin((1-u)\theta)}{\sin\theta}q_0+
\frac{\sin(u\theta)}{\sin\theta}q_1.$$

The implementation handles near-equal inputs and rotation sign equivalence. Smooth rotation is a presentation operation unless the model itself defines orientation as state; it does not prove smooth curvature change or new observations.

### What the displayed $J^TJ$ actually is

`council_latent_linear.py` fits an affine one-step map to recent past transitions, pooled within the selected domain:

$$J=[(X_c^TX_c+\lambda I)^{-1}X_c^TY_c]^T,\quad
\widehat z_{t+1}=Jz_t+b.$$

It is a ridge fit, **not automatic differentiation of the simulator**. Identity-changing slot transitions are excluded. $G=J^TJ$ is symmetric positive semidefinite since $v^TGv=\|Jv\|^2$; its eigenvalues are squared singular gains per recorded step. Inactive coordinates in the observed/hidden four-coordinate maps are true zeros, so their embedded $8\times8$ matrices have rank at most four. Do not identify $G$ with a continuous-time Jacobian, a universally positive-definite metric, or the separate curvature metric.

### What generates the curvature diagnostic

`council_curvature.py:metric_jet` declares a smooth metric on a chosen four-dimensional chart using projected recorded contact-edge directions $u_e$ and midpoints $m_e$:

$$g_{ab}(x)=\delta_{ab}+\frac{s}{M}\sum_e
e^{-\|x-m_e\|^2/(2h^2)}u_{e,a}u_{e,b}.$$

Defaults are $s=1,h=0.8$. Unit directions and kernels bounded by one imply $I\preceq g\preceq(1+s)I$. No usable edges yields the identity prior, not empirical evidence of flat social structure. The combined chart uses initial fixed 4D PCA; observed/hidden charts use fixed coordinate selectors and initial scaling.

Analytic spatial derivatives feed the Levi-Civita connection

$$\Gamma^a_{bc}=\frac12g^{ad}(\partial_bg_{dc}+\partial_cg_{db}-\partial_dg_{bc}),$$

then Riemann curvature, Ricci contraction, and Weyl curvature. In dimension four, Weyl subtracts the Ricci and scalar traces from Riemann. The six independent bivectors are oriented plane elements $e_1\wedge e_2,e_1\wedge e_3,e_1\wedge e_4,e_2\wedge e_3,e_2\wedge e_4,e_3\wedge e_4$. Whitening by $g^{-1/2}$ makes the six-by-six displayed operator refer to an orthonormal frame. Its entries describe that constructed metric's trace-free curvature, not interactions between six citizens.

This metric is neither induced by the Hopf image nor $J^TJ$. The round $S^4$ has constant sectional curvature and zero Weyl tensor; a curved embedding does not imply nonzero Weyl curvature. Separate sphere/ellipsoid routines must be read under their own definitions. None of these calculations is Einstein's field equation, Ricci flow, a measured spacetime curvature, or a demonstrated causal law of governance.

The [geometry derivation appendix](#geometry-appendix-what-the-matrices-actually-measure) supplies the ridge normal equations, analytic metric derivatives, Riemann/Weyl sign convention, ellipsoid Gauss equation, normalized response map, and the exact matched-round $\Delta G$. In particular, that $\Delta G$ compares the same recorded snapshot with unit-round axes; it is not a temporal difference between two successive heatmaps.

## Reproducibility and claims a repository can support

State the seed, release, configuration, identities, selected interval, cohort, belief coordinate, cutoff convention, and metric domain alongside results. Preserve incoming topology with each copied-flow record. For stochastic comparisons, use declared seed ensembles, timestep refinement, and held-out scenarios; a visually smooth trajectory is not a validation study.

Useful numerical checks include $B_1B_2=0$, reconstruction and projection residuals, orthogonality, nonnegative Laplacian eigenvalues within tolerance, bounded-state invariants, normalized walk/culture sums, and election receipt replay. For the full coupled model, also test sensitivity to step size and policy coefficients. Passing these establishes implementation properties, not empirical accuracy.

Citizen chat can summarize the selected person's recorded situation, capabilities, beliefs, and history. It should distinguish a generated conversational interpretation from a numerical state or verified new fact. A fluent answer does not independently validate the model or make a simulated competency a measured cognitive ability.

### Source map

| Mathematical object | Authoritative implementation |
|---|---|
| Initial states, contact complex, spread, policy integration | `council_network.py:_make_agents`, `build_complex`, `spread_step`, `_sense`, `_review`, `_propose`, `_apply` |
| Eight-vector and mismatch gate | `council_turnover.py:shared_latent`, `latent_comparison`, `TurnoverGate.advance` |
| Crystal PCA and hull | `council_crystal.py:_fixed_projection`, `project_latent`, `_hull` |
| Distinct belief embedding / latent neighbors | `council_belief_view.py:belief_latent_frame`; `council_latent_connectivity.py:_neighbours` |
| Empirical belief terrain, candidate groups, separate OU probe | `belief_landscape_ui.js:project`, `density`, `contrastGrid`, `graph`, `cohortTransition`, `ouState`, `ouGrid` |
| Recorded copying and two-way reconstruction | `council_nuance.py:NuanceEngine._transport`, `transport_directional_flux` |
| Sparse Hodge decomposition and observatory scope | `council_hodge_network.py:boundary_operators`, `decompose_edge_flow`, `analyse_network_hodge` |
| Fuzzy mechanisms and auditable explanations | `council_fuzzy_explain.py:MECHANISMS`, `review_breakdown`, `confidence_breakdown` |
| Competencies, culture, succession | `council_civilization.py:CivilizationEngine.advance`, `CAPABILITIES` |
| Democratic election rule and receipts | `council_elections.py:prepare_election` and receipt computation |
| Quaternion operations, Hopf, interpolation | `council_geometry.py:hopf_s4`, `quaternion_slerp`, `geometry_frame` |
| Fitted affine dynamics / declared metric curvature | `council_latent_linear.py`; `council_curvature.py:metric_jet`, `curvature` |

### References

These sources support the mathematical methods. They do not validate the application's social parameters or demonstrate governance efficacy.

1. Lim, L.-H. (2020). *Hodge Laplacians on Graphs*. SIAM Review 62(3), 685–715. [Author manuscript](https://arxiv.org/abs/1507.05379). [DOI](https://doi.org/10.1137/18M1223101).
2. Jiang, X., Lim, L.-H., Yao, Y., & Ye, Y. (2011). *Statistical Ranking and Combinatorial Hodge Theory*. Mathematical Programming 127, 203–244. [Author manuscript](https://arxiv.org/abs/0811.1067). [DOI](https://doi.org/10.1007/s10107-010-0419-x).
3. GUDHI Project. [Rips complex documentation](https://gudhi.inria.fr/python/latest/rips_complex_user.html). Check its cutoff convention when comparing software.
4. GUDHI Project. [Čech complex documentation](https://gudhi.inria.fr/doc/latest/group__cech__complex.html). Ball-radius conventions must be made explicit.
5. Zadeh, L. A. (1965). *Fuzzy Sets*. Information and Control 8(3), 338–353. [DOI](https://doi.org/10.1016/S0019-9958(65)90241-X).
6. Baez, J. C. (2002). *The Octonions*. Bulletin of the American Mathematical Society 39, 145–205. [Author manuscript](https://arxiv.org/abs/math/0105155).

**Suggested public description:** “AIGovernanceCouncil is an experimental agent-based governance simulation combining bounded belief dynamics, explicit voting rules, geometric embeddings, and Hodge/topological diagnostics. Its purpose is to make modeled assumptions and interactions inspectable; real-world validity and comparative effectiveness remain research questions.”


---

# Geometry appendix: what the matrices actually measure

Source audit: AIGovernanceCouncil / HyperCouncil **v8.87.0**, supplied `app/` source. This appendix describes implemented diagnostics, not a derivation of social behavior from physical laws. Descriptor coordinates and model time are dimensionless.

## 1. Keep three mathematical objects separate

| Object | Construction | Meaning |
|---|---|---|
| Eight-dimensional response Gram matrix | Fitted population map, $G=J^\top J$ | Squared fitted one-step descriptor gains |
| Conditional four-dimensional metric | Gaussian-weighted recorded edge directions plus identity | A declared model whose Levi-Civita curvature is computed |
| Sphere/ellipsoid probe | Normalize selected coefficients, then prescribe positive axis lengths | Induced intrinsic geometry of that chosen hypersurface |

None is the Hodge edge Laplacian. None automatically identifies the geometry of a real society or an LLM's internal activations.

Current preparation calls `council_latent_linear.build_latent_linear` and `council_curvature.build_geometry` through `council_prepared.py`. Selected-agent sphere results come from `council_sphere_curvature.diagnostic_snapshot`, exposed by `/api/network/sphere-curvature` in `council_app.py`. Older benchmark descriptors in other experiments should not be substituted for the network's recorded `latent` arrays.

## 2. Derive the actual 8 × 8 matrix

The network records eight simulator descriptors per occupant. For each completed transition, form row observations $x=z_{k-1}$, $y=z_k$. Keep a pair only when the same identity occupies the same role slot at both endpoints. Pool eligible observations across the trailing eight transitions by default; no future transition enters the fit.

Center the observation matrices:

$$
X_c=X-\mathbf1\bar x^\top,\qquad Y_c=Y-\mathbf1\bar y^\top.
$$

Minimize ridge-regularized squared prediction error, leaving the intercept unpenalized:

$$
\min_{J,b}\|Y-XJ^\top-\mathbf1b^\top\|_F^2+\lambda\|J\|_F^2.
$$

The normal equations yield

$$
J=\left[(X_c^\top X_c+\lambda I)^{-1}X_c^\top Y_c\right]^\top,
\qquad b=\bar y-J\bar x.
$$

Here $E=\operatorname{tr}(X_c^\top X_c)/8$ and
$\lambda=\max(10^{-3}E,\epsilon_{\rm machine}\max(E,1))$ with default controls. The implementation solves the linear system rather than explicitly inverting it. Rank-zero input is unavailable; lower-rank input is regularized and labeled accordingly.

For a perturbation $v$,

$$
v^\top Gv=\|Jv\|^2\ge0,\qquad G=J^\top J,
\qquad \lambda_i(G)=\sigma_i(J)^2.
$$

This explains the heatmap and singular gains. **J is a fitted affine surrogate, not the analytic Jacobian of the full coupled simulator.** It is per recorded step, not divided by the timestep. It does not establish a Lyapunov exponent, causality, or an intrinsic curvature metric. Descriptor scaling affects it. A retrospective holdout refits preceding blocks and evaluates the latest completed block; agents may recur on both sides.

Observed-domain indices `[1,3,6,7]` and hidden-domain indices `[0,2,4,5]` produce separate fits with inactive coordinates zeroed. These are four-active-channel maps embedded in eight labels, so rank is at most four. Zero cells are meaningful exclusions, not missing invented dimensions.

Source: `council_latent_linear.py`, `_fit_affine`, `_build_latent_linear`, `build_latent_linear`.

## 3. Construct the separate four-dimensional chart

For the combined view, fit a four-row orthonormal PCA basis $P$ and origin $\mu_0$ once on the initial eight-dimensional population. With initial projected RMS scale $s_0>0$, subsequent points are

$$
p_i=(z_i-\mu_0)P^\top/s_0.
$$

The anchor remains fixed across continuation. The observed and hidden views instead use their four selected raw channels and their own initial RMS scales. These choices make comparisons conditional on the chart and scale. A low-rank cloud is not promoted into evidence for an intrinsic four-dimensional manifold.

For each cohort, take its recorded internal pairwise edges. For a nonzero-length edge $e=(i,j)$, define its midpoint $m_e=(p_i+p_j)/2$ and unit direction $u_e=(p_j-p_i)/\|p_j-p_i\|$. The metric is

$$
g_{ab}(x)=\delta_{ab}+\frac{\eta}{M}\sum_e
\exp\left[-\frac{\|x-m_e\|^2}{2h^2}\right]u_{e,a}u_{e,b},
$$

with defaults $h=0.8,\eta=1$, evaluated at the cohort centroid. Since each outer product is positive semidefinite and its unit-vector directional contribution is at most one,

$$
1\le\lambda_i(g)\le1+\eta.
$$

If no usable edges remain, the metric is the Euclidean **prior** $I$; this is not evidence that the data are intrinsically flat.

Let $r_e=m_e-x$ and $k_e=\eta\exp[-\|r_e\|^2/(2h^2)]/M$. Exact spatial derivatives are

$$
\partial_c k_e=k_er_{e,c}/h^2,\qquad
\partial_c\partial_d k_e=k_e(r_{e,c}r_{e,d}/h^4-\delta_{cd}/h^2).
$$

Insert these into the edge sum for $\partial g$ and $\partial^2g$. The production curvature calculation uses these analytic derivatives, not fitted finite differences.

Source: `council_curvature.py`, `projection_anchor`, `domain_anchor`, `metric_jet`.

## 4. From a metric to Ricci and Weyl

The Levi-Civita connection is

$$
\Gamma^a_{bc}=\tfrac12g^{ad}
(\partial_b g_{dc}+\partial_c g_{db}-\partial_d g_{bc}).
$$

Differentiate it using $\partial g^{-1}=-g^{-1}(\partial g)g^{-1}$, then compute

$$
R^a{}_{bcd}=\partial_c\Gamma^a_{db}-\partial_d\Gamma^a_{cb}
+\Gamma^a_{ce}\Gamma^e_{db}-\Gamma^a_{de}\Gamma^e_{cb},
$$
$$
R_{abcd}=g_{ae}R^e{}_{bcd},\quad
\mathrm{Ric}_{bd}=g^{ac}R_{abcd},\quad R=g^{bd}\mathrm{Ric}_{bd}.
$$

This convention gives positive scalar curvature for a positively curved round sphere. For dimension $n\ge3$, remove Ricci and scalar traces:

$$
W_{abcd}=R_{abcd}-\frac{g_{ac}\mathrm{Ric}_{bd}-g_{ad}\mathrm{Ric}_{bc}-g_{bc}\mathrm{Ric}_{ad}+g_{bd}\mathrm{Ric}_{ac}}{n-2}
+\frac{R(g_{ac}g_{bd}-g_{ad}g_{bc})}{(n-1)(n-2)}.
$$

Weyl measures the trace-free conformal part of curvature, not every form of anisotropy or change. In three dimensions it vanishes identically; conformal curvature there requires the Cotton tensor, which this probe does not compute.

The four-dimensional calculation converts tensors into an orthonormal frame using $g^{-1/2}$. Its bivector basis contains the six oriented coordinate planes
$12,13,14,23,24,34$. A bivector represents an oriented area, rather than another agent or scalar trait. The displayed operator has entries
$\mathcal W_{(ab),(cd)}=W^{\rm orth}_{abcd}$ for $a<b,c<d$, hence **6 × 6**. The reported Weyl norm is the full four-index tensor norm, twice the Frobenius norm of this ordered-pair matrix.

The separate Gaussian readout restricts the metric and its derivatives to a two-coordinate surface and recomputes its intrinsic curvature, $K=R_{2D}/2$. It is not generally ambient sectional curvature or curvature of the displayed three-dimensional crystal. Temporal Ricci changes compare recorded snapshot matrices; they are not a Ricci-flow solution or a covariant time derivative obtained by parallel transport.

Source: `council_curvature.py`, `curvature`, `gaussian_slice`, `_build_geometry_domain`.

## 5. Sphere and ellipsoid geometry

Normalize a nonzero selected coefficient vector: $q=z/\|z\|$. Four coefficients give $S^3$; eight give $S^7$, with intrinsic dimension one less than coefficient count. Prescribe

$$
x=A(t)q,\quad A=\operatorname{diag}(a_i),\quad
a_i=r\exp\{\alpha\sin\theta+\beta\sin(\theta+2\pi i/(n+1))\},
\quad\theta=2\pi t/T+\phi.
$$

The positive axes define an ellipsoid in flat Euclidean ambient space. Its intrinsic curvature can be nonzero despite ambient flatness. With $Q=A^{-2}$, unit normal $\nu=Qx/\|Qx\|$, and orthonormal tangent frame $E$, the shape operator is

$$
S=E^\top QE/\|Qx\|.
$$

The Euclidean hypersurface Gauss equation yields

$$
R_{abcd}=S_{ac}S_{bd}-S_{ad}S_{bc},\qquad
\mathrm{Ric}=\operatorname{tr}(S)S-S^2.
$$

These convex ellipsoids have positive principal and sectional curvatures; they do not model arbitrary saddle geometry. On a radius-$r$ round sphere,
$S=I/r$, $\mathrm{Ric}=(n-1)I/r^2$, $R=n(n-1)/r^2$, and $W=0$. In particular, round **S4 also has zero Weyl curvature**. S7 has $\binom72=21$ bivector directions and a 21 × 21 operator; S3 has three and identically zero Weyl.

For the matched response probe, hold the recorded point, fitted J and axes fixed while differentiating
$F(\delta)=A\,\mathrm{normalize}(z_{\rm active}+J_{\rm active,:}\delta)$. Then

$$
D=\frac{(I-qq^\top)J_{\rm active,:}}{\|z_{\rm active}\|},\quad
B=AD,\quad G_A=D^\top A^2D,\quad
\Delta G=D^\top(A^2-I)D.
$$

Thus ΔG compares the same snapshot against **unit round axes**, not the preceding timestep. Normalization removes a radial direction; it creates no information or rank. Missing norms/fits remain unavailable.

Source: `council_sphere_curvature.py`, `ellipsoid_geometry`, `response_diagnostic`, `diagnostic_snapshot`.

## 6. Hopf maps, animation, and interpretation

`council_geometry.hopf_s4` normalizes all eight coefficients once and splits them into Hamilton quaternions $(a,b)$. It computes
$H(a,b)=(2a\bar b,\|a\|^2-\|b\|^2)\in S^4$: the **quaternionic Hopf map S7 → S4**, not the octonionic Hopf map S15 → S8. These recorded-state charts do not implement an octonion decision policy. Their S4 image is separate from the conditional four-dimensional metric above.

SLERP smoothly interpolates unit directions/orientations. It does not generate additional measured states or solve curvature evolution. The ellipsoid probe is algebraic at each time, not a PDE integrator; no CFL condition applies. Its source recommends at least 20 samples per modulation period. A 55–60 FPS renderer is a display goal, not evidence for that many independent simulation observations per second.

## Verification scope

The reproducible script [verification_geometry.py](verification_geometry.py) passed **20/20** source-based checks; [GEOMETRY_QA.json](GEOMETRY_QA.json) records errors, tolerances and source hashes. Checks cover metric bounds, analytic first/second derivatives, curvature identities, round S3/S4/S7, an independent anisotropic S4 oracle, response derivatives/rank, matched ΔG, Gram/SVD equality and the Hopf unit-sphere identity. Reproduce with:

```bash
python verification_geometry.py --app /path/to/release/app
```

The existing pytest suite was not run in this audit because pytest was unavailable in the audit interpreter. These algebraic checks do not validate sociological predictions.


---

# Exact triangle verification

The accompanying `verify_hodge_exact.wl` was evaluated through the Wolfram Language tool during this preparation. It uses exact integers and pseudoinverses, independently of the application's sparse solver and the HTML's JavaScript solver.

Canonical edges are AB, AC and BC. For f = (3, −2, 1), the returned result is:

| Quantity | Exact result |
|---|---|
| B₁B₂ | Zero column |
| Gradient | (1, 0, −1) |
| Filled-triangle curl | (2, −2, 2) |
| Filled-triangle harmonic | (0, 0, 0) |
| Unfilled-triangle harmonic | (2, −2, 2) |
| Filled L₁ eigenvalues | 3, 3, 3 |
| Unfilled L₁ eigenvalues | 3, 3, 0 |
| Squared norms: total, gradient, curl, harmonic | 14, 2, 12, 0 |
| Pairwise component dot products | 0, 0, 0 |

This verifies the algebra of the worked example. It does not validate the simulation's social assumptions or make its squared flow norms physical energy.

Wolfram documentation: [PseudoInverse](https://reference.wolfram.com/language/ref/PseudoInverse.html), [Eigenvalues](https://reference.wolfram.com/language/ref/Eigenvalues.html).
