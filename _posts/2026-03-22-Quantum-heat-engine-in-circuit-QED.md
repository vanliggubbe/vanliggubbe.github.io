---
layout: post
title: "Quantum heat engine in circuit&nbsp;QED"
date: 2026-03-22
tags: [cQED, quantum thermodynamics]
---

Recently, we've got published a theoretical paper[^rasola2025] about a quantum heat engine in microwave superconducting circuits. And last week we put experimental work on arXiv[^uusnaekki2026]!

<figure>
  <img src="{{'/images/qhe-amogus.webp' | relative_url}}" style="max-width:35%; height:auto; display: block; margin: 0 auto;" alt="Amogus">
  <figcaption style="text-align:center;font-size:0.9em;">
    Cool amogus plot from the theory paper.
  </figcaption>
</figure>

<!--more-->

What is the paper about? Long story short, we made low-frequency (~100--300&nbsp;MHz) cryogenic microwave source driven by the temperature difference. It implements a thermodynamic Otto cycle[^kosloff2017], it's schematic shown in Figure below:
<figure>
  <img src="{{'/images/qhe-otto-cycle.svg' | relative_url}}" style="max-width:90%; height:7cm; display: block; margin: 0 auto;" alt="Otto cycle">
  <figcaption style="text-align:center;font-size:0.9em;">
    Schematic of an Otto cycle.
  </figcaption>
</figure>
The role of working fluid is played by a tunable-frequency harmonic oscillator. The cycle consists of four strokes:
1. Adiabatic compression (frequency increase)
2. Isochoric heating
3. Adiabatic expansion (frequency decrease)
4. Isochoric cooling

Number of photons in the oscillator is an adiabatic invariant and remains constant during compression/expansion strokes.
During the heating, we populate the oscillator with photons and deplete it during the cooling stroke.
Since we pump the oscillator with high energy (frequency) photons and the energy (frequency) of the photons absorbed by the cold bath is low photons, the net work produced by heat engine is positive.

How do we make this cycle self-sustained? We introduce a slow and heavy "flywheel" oscillator and provide optomechanical coupling to the 
working fluid: former \\(X(t)\\) tunes the frequency of the latter \\(\omega(t) = \omega_0 + \eta X(t)\\).
Then, this flywheel mode can accumulate the useful work. At the same time, oscillations of flywheel enable compression and expansion strokes of the cycle.

<figure>
  <img src="{{'/images/qhe-flywheel.svg' | relative_url}}" style="max-width:100%; height:7cm; display: block; margin: 0 auto;" alt="Flywheel">
  <figcaption style="text-align:center;font-size:0.9em;">
    Flywheel motion vs. strokes of the cycle.
  </figcaption>
</figure>

The last issue is the realization of the heating and cooling strokes.
Turning the coupling on and off with hot and cold baths in sync with the motion of the flywheel seems to be quite challenging.
Instead, we couple both baths to the working mode through narrow-band filters. Then, the working mode couples to the bath only if its frequency is the close to the filter frequency. During the engine operation, the frequency of the working mode oscillates and couples to one of the baths in the extreme points.

<figure>
  <img src="{{'/images/qhe-narrow-band-baths.svg' | relative_url}}" style="max-width:100%; height:7cm; display: block; margin: 0 auto;" alt="Narrow-band baths">
  <figcaption style="text-align:center;font-size:0.9em;">
    Lorentz-shaped cold and hot bath spectral densities. Working fluid frequency at rest is shown in the middle.
  </figcaption>
</figure>

And it works! We were able to detect a tremendous 2&nbsp;aW of output power from the engine. The poor phase stability also rules it out as any practical microwave source. But hey, it's a proof of principle work. No parameters were optimized, and I believe there is a huge room for improvement of this engine as a microwave generator.

What would be really cool is to make it even "more quantum". In current design, mean number of photons in the working mode is of the order of unity, but that's pretty much all the "quantumness" we have (and it plays against us). I wonder how to introduce quantum coherence between different parts of the engine and how it would affect its performance.

## References
{:footnotes}

[^rasola2025]: [M. Rasola, V. Vadimov, T. Uusnäkki, and M. Möttönen, “Proposal for an autonomous quantum heat engine,” SciPost Physics, vol.&nbsp;19, no.&nbsp;4, p.&nbsp;101, Oct.&nbsp;2025](https://doi.org/10.21468/scipostphys.19.4.101)
[^uusnaekki2026]: [T. Uusnäkki, M. Rasola, V. Vadimov, P. Singh, A. Darwish, and M. Möttönen, "Autonomous Quantum Heat Engine",&nbsp;arXiv:2603.15355](10.48550/ARXIV.2603.15355)
[^kosloff2017]: [R. Kosloff and Y. Rezek, “The Quantum Harmonic Otto Cycle,” Entropy, vol. 19, no. 4, p. 136, Mar. 2017](https://doi.org/10.3390/e19040136)
[^rasola2024]: [M. Rasola and M. Möttönen, “Autonomous quantum heat engine based on non-Markovian dynamics of an optomechanical Hamiltonian,” Scientific Reports, vol. 14, no. 1, p. 9448, Apr. 2024](https://doi.org/10.1038/s41598-024-59881-z)
