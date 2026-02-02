---
layout: post
title: "More units and less units"
date: 2026-02-01
tags: [numerical methods, dimensional quantities]
---

I work in an experimental group and sometimes do numerics with dimensional quantities. A typical example could be an electric quantum circuit with all the capaticances,
inductances, resistances/impedances given as dimensional numbers, and I need to calculate scattering matrices or spectrum of the Hamiltonian. My colleagues also do lots of data
analysis and manipulate dimensional data.

Very often I see people doing these calculations in base SI units for respective physical quantities: time in seconds, capacitance in Farads, and so on.
But in superconducting electronics it's an absolutely horrible choice: typical time scales are nano- or microseconds, magnetix flux is measured in flux quanta, and so on.
And who the hell knows, without doing arithmetics, what's the energy of the 5&nbsp;GHz photon in joules?

<!--more-->

First of all, there are libraries which allow to operate with dimensional units. In python it's [`pint`](https://pint.readthedocs.io/en/stable/),
in Julia it's [`Unitful.jl`](https://github.com/JuliaPhysics/Unitful.jl). If you ever write code that works with dimensional data, please use them.
It saves a lot of effort on unit conversion and doesn’t let you do something stupid like adding quantities of different dimensions. But if you need to do some involved numerics beyond simple arithmetic, it’s more convenient to work with bare floating-point numbers.

I've adopted the following approach to deal with this problem:
1. Pick a few reference unitful quantities. For example, in circuit QED
   it's natural to use reference charge \\(q_\mathrm{ref} = 2e\\), action \\(S_\mathrm{ref} = \hbar\\), and energy-temperature ratio \\(E_\mathrm{ref} / T_\mathrm{ref} =
   k_\mathrm B\\). Then I
   pick my favorite frequency, for example \\(\omega_\mathrm{ref} = 2\pi \times
   1~\mathrm{GHz}\\). 
   This choice is dictated purely by convenience: I don’t want meaningful numbers to look like floating-point arithmetic errors.
2. Then I introduce derived units, like units of
   voltage \\(V_\mathrm{ref} = \Phi_\mathrm{ref} \omega_\mathrm{ref}\\), current
   \\(I_\mathrm{ref} = q_\mathrm{ref}  \omega_\mathrm{ref}\\), capacitance 
   \\(C_\mathrm{ref} = q_\mathrm{ref} / V_\mathrm{ref}\\), inductance \\(L_\mathrm{ref} = \Phi_\mathrm{ref} / I_\mathrm{ref}\\),
   energy \\(E_\mathrm{ref} = S_\mathrm{ref} \omega_\mathrm{ref}\\), and so on. Now I can drop dimensions from any relevant quantity by
   dividing it by respective unit.
3. When I'm done with all the numerics and get an answer, I make it back dimensional by multiplying by corresponding unit.

## LessUnits.jl

To make my life easier, I wrote a small Julia library
[`LessUnits.jl`](https://github.com/vanliggubbe/LessUnits.jl) which simplifies
evaluation of derived units and dimension reduction. Here is an example of
calculating spectrum of a transmon qubit. First, I define reference units:
```julia
julia> using LinearAlgebra, Unitful, LessUnits

julia> refs = (2u"q", 1u"ħ", 2π * 1u"GHz")
(3.204353268e-19 C, 1.0545718176461565e-34 J s, 6.283185307179586 GHz)
```
Then specify parameters of the qubit: capacitance and Josephson energy. I define latter through the junction critical current,
since the factor \\(\Phi_0 / (2\pi)\\) is a unit of flux.
```julia
julia> C = unitless(refs, 200u"fF")
1.2906403729652256

julia> Ej = unitless(refs, 40u"nA")
19.867340431067024
```
Construct the Hamiltonian in the charge basis, cropped to 21 levels and find it's eigenvalues:
```julia
julia> H = SymTridiagonal((-10 : 10) .^ 2 / (2.0 * C), fill(-Ej / 2.0, 20));

julia> E = eigvals(H)
21-element Vector{Float64}:
 -17.930142746697705
 -14.106112977374295
 -10.38500869843966
  -6.771561125465437
  -3.2712569490979746
   0.10954942236079235
   3.3636143347524325
   6.4828418753985
   9.45754535934299
  12.27654471247287
  14.89460554150971
  17.39544008266507
  19.039577083059577
  22.177480079281747
  22.39152244008497
  27.986720413538666
  27.990432684380377
  35.948460990208766
  35.94847441929393
  47.65140232678857
  47.6514023316866
```
Get the frequency and anharmonicity of the transmon:
```julia
julia> (E[2] - E[1]) * unitof(u"GHz", refs) / (2π)
3.8240297693234098 GHz

julia> (E[3] - 2 * E[2] + E[1]) * unitof(u"GHz", refs) / (2π)
-0.10292549038877752 GHz
```

Of course, the library allows to use arbitrary 
reference values&nbsp;--- you don't need to stick to
my favorite choice of units. The library is not yet registered, but it will be
soon.

**PS** No surprise, this idea is not new. You can also check out [`Dimensionless.jl`](https://github.com/martinkosch/Dimensionless.jl), which does a similar thing.
