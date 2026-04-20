---
layout: post
title: "What the HEOM"
date: 2026-04-20
tags: [numerical methods, open quantum systems]
---

There are quite a number of numerical methods for simulating open quantum system
dynamics. Unfortunately, pretty much all of them are quite involved
technically. The approach I'm the most familiar with is hierarchical equations of motion, or
HEOM[^tanimura2020]. Still, the more I work with it, the more I feel that I don't understand a single thing about HEOM.
So, I'd like to complain about the difficulties and subtleties of
this approach.
<!--more-->

HEOM is a first order differential equation in time for
an infinite hierarchy of auxiliary density matrices \\(\rho_\mathbf n\\), where \\(\mathbf n\\) is 
a multiindex of non-negative integers. The physical reduced density matrix of
the system is given by top of the hierarchy \\(\mathbf n = \mathbf 0\\). The
physical meaning of other components is less transparent: they contain system--bath correlations, extracting some meaningful quantities is possible but not so trivial[^vadimov2025].

<figure>
  <img src="{{'/images/heom.svg' | relative_url}}" style="max-width:90%; height:7cm; display: block; margin: 0 auto;" alt="Hierarchical equations of motion">
  <figcaption style="text-align:center;font-size:0.9em;">
    Hierarchy of auxiliary density matrices. The lines denote couplings between the hierarchy levels in HEOM.
  </figcaption>
</figure>

## Where does it come from
In my opinion, the conceptually simplest derivation is presented in Ref[^xu2023]. First of all, one needs to get an approximation of correlation function as a sum of exponentials:
\\[
    C(t) = \sum\limits_{k=1}^K d_k e^{-z_k t},~t > 0.
\\]
The coefficients \\(d_k\\) and relaxation constants \\(z_k\\) can in principle be complex. This allows a great freedom in choice of parameters: even quite nasty correlation functions can be approximated with reasonably few terms[^xu2022]. Then, each of the exponential terms yields two indices in the hierarchy, one corresponds to the auxiliary mode with decay rate \\(z_k\\) and another with its complex conjugate. One can introduce bosonic ladder operators which change one index of the hierarchy and write the generator of the equation (Liouvillian) in the operator form. My coauthors even coined a name QD-MESS, probably because it looks like a complete mess.

## HEOM, what's wrong with you?

### Some approximations are better than other
The first concerning thing is related to exponential approximation. By applying Fourier or Laplace transformation to the correlation function, we reduce the problem to the rational approximation. In Ref[^xu2022], they suggest to use AAA algorithm[^nakatsukasa2018] directly to noise spectral density \\(S(\omega)\\). The thing is, this is not the only way to do it. One can recall that
\\[
    S(\omega) = \frac{1}{2}J(\omega)\left[\coth\left(\frac{\omega}{2T}\right) + 1\right]
\\]
with spectral density \\(J(\omega)\\) being an odd function of frequency. Then, one can approximate separately \\(J(\omega)\\) and \\(J(\omega) \coth[\omega/(2T)]\\) _enforcing_ their odd and even symmetries using slightly modified version of AAA algorithm. This will yield an approximation with nearly same number of exponentials, but a bunch of them are going to be real: \\(\operatorname{Im}z_k = 0\\). For such terms it's enough to introduce a _single_ hierarchy index. And number of indices in HEOM matters a lot because of the computational resources! Moreover, if \\(J(\omega)\\) is nice enough, most of the exponential terms come from \\(\coth[\omega/(2T)]\\) and have real decay rate.

### Truncation and stability
Regardless of the number of indices, the hierarchy is infinite. Which means that one needs to truncate it when it comes to numerical solving. There are number of ways to do it, the most popular choices are: take account of \\(\mathbf n\\) for which (a) \\(\sum_j n_j \leqslant N\\) or (b) \\(\max_j n_j \leqslant N\\), for some positive integer \\(N\\). Turns out, the truncated HEOM may become unstable and it's generator may have eigenvalues with positive real parts[^dunn2019][^krug2023].

For the finite dimensional open quantum systems this question can be cleared out a bit. The coupling between the hierarchy levels is dominated by diagonal decay at sufficiently deep hierarchy levels, which enables perturbative analysis. As a result, pretty much any reasonable truncation approach converges to the exact HEOM. In practice, one may need to take account of really many hierarchy levels so that brute force simulation becomes unfeasible. I'm currently finalizing a paper on this topic righ now, hopefully it's gonna be out on arXiv soon.

### Metric

In Hilbert space of pure quantum states, there is a natural choice of metric given by the inner product. For the density matrix operators the natural norm choice is a trace norm \\( \left\| \hat \rho \right\| = \operatorname{Tr} \sqrt{ \hat \rho^\dagger \hat \rho}\\). What about the norm of the ADO hierarchy? This is not just an abstract question, for example one can solve HEOM using McLachlan time-dependent variational principle, then choice of the norm affects the resulting solution.

The most convenient norm for numerical analysis is a (weighted) \\( \ell_2\\) norm, which also implies Frobenius norm for the density operators. It is problematic for infinite-dimension systems: even the trace operation becomes discontinuous. And the results of numerical calculations depend _a&nbsp;lot_ on the mutual choice of weight and truncation.

### Positivity

Density matrix which corresponds to a physical state must be positive semidefinite. Lindblad equation, for example, preserves this property. What about HEOM? How does one even define positivity for the hierarchy of ADOs?

This and previous questions are implicitly addressed in a recent preprint[^mueller2026]. There, the authors show that HEOM with symmetric indices can be mapped to a Lindblad equation, provided the rational approximation to the correlation function is positive. And Lindblad equations has no such problems! The only remaining question here is what to do with purely decaying exponential terms? One can of course double them as well and later map to Lindblad, but it seems like unnecessary complication.

Long story short, I believe there is still quite a bit to learn about the formalism itself. And it should help to make less stupid mistakes when trying to solve something with its help.

## References
{:footnotes}

[^tanimura2020]: [Y. Tanimura, Numerically “Exact” Approach to Open Quantum Dynamics: The Hierarchical Equations of Motion (HEOM), The Journal of Chemical Physics 153, (2020)](https://doi.org/10.1063/5.0011599)
[^vadimov2025]: [V. Vadimov, M. Xu, J. T. Stockburger, J. Ankerhold, and M.
    Möttönen, Nonlinear-Response Theory for Lossy Superconducting Quantum
    Circuits, Physical Review Research 7, 13317
    (2025)](https://doi.org/10.1103/physrevresearch.7.013317)

[^xu2023]: [M. Xu, V. Vadimov, M. Krug, J. T. Stockburger, and J. Ankerhold, A
    Universal Framework for Quantum Dissipation:Minimally Extended State Space
    and Exact Time-Local Dynamics](https://doi.org/10.48550/ARXIV.2307.16790)

[^xu2022]: [M. Xu, Y. Yan, Q. Shi, J. Ankerhold, and J., Taming Quantum Noise for Efficient Low Temperature Simulations of Open Quantum Systems, Physical Review Letters 129, 230601 (2022)](https://doi.org/10.1103/physrevlett.129.230601)

[^nakatsukasa2018]: [Y. Nakatsukasa, O. Sète, and L. N. Trefethen, The AAA Algorithm for Rational Approximation, SIAM Journal on Scientific Computing 40, A1494 (2018)](https://doi.org/10.1137/16m1106122)

[^dunn2019]: [I. S. Dunn, R. Tempelaar and D. R. Reichman, Removing instabilities in the hierarchical equations of motion: Exact and approximate projection approaches, The Journal of Chemical Physics 150(18) (2019)](https://doi.org/10.1063/1.5092616)

[^krug2023]: [M. Krug and J. Stockburger, On stability issues of the heom method, The European Physical Journal Special Topics 232(20–22), 3219 (2023)](https://doi.org/10.1140/epjs/s11734-023-00972-9)

[^xu2026]: [M. Xu, V. Vadimov, J. T. Stockburger, and J. Ankerhold, Simulating
    Non-Markovian Dynamics in Open Quantum
    Systems.](https://doi.org/10.48550/ARXIV.2601.02160)

[^mueller2026]: [K. Müller and W. T. Strunz, One-to-One Correspondence between Hierarchical Equations of Motion and Pseudomodes for Open Quantum System Dynamics](https://doi.org/10.48550/ARXIV.2604.06466)
