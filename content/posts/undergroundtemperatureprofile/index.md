+++
title = 'Underground temperature profile'
date = 2025-05-15T19:12:33+02:00
draft = true
math = true
+++

I once came across a graph somewhere of the underground soil temperature, measured at different depth and times of the year, and something about it surprised me a little. As expected the fluctuations in temperature mirror the temperature at the top level, becoming smaller as you go deeper, but there also seems to be a growing *lag* between the temperature underground and the one at the surface.

{{< figure width=400 align="center" src="cui.svg" caption="Reproduced from *Measurement and Prediction of Undisturbed Underground Temperature Distribution* by Cui et al. [doi.org/10.1115/IMECE2011-63311](https://doi.org/10.1115/IMECE2011-63311)" >}}

Of course it's not so weird that in some circumstances the temperature profile isn't monotonously increasing or decreasing, obviously a shift in the boundary condition that is shortly after reversed should leave behind a temperature profile that's not just monotonously increasing or decreasing but one with a bump, but I was mildly surprised to see it persist in the steady-state. I'm not entirely sure where my surprise comes from, perhaps intuitively I felt first-order mechanics shouldn't be able to sustain oscillations or traveling waves.

Sure, Schrödinger's equation might seem like an exception at first sight, as it is first-order in time and allows for (dispersive) wavelike mechanics. But due to the complex nature of the wavefunction and the $i$ preceding the time derivative, the mechanics for the real and imaginary parts are in fact second-order in disguise.

Still, I don’t think anything more exotic than classical heat conduction is at play here, so we can numerically solve the heat equation in Mathematica with a simple sine wave as a boundary condition, to get a very similar looking profile once the transient effects settle:

{{< figure align="center" src="ndsolve.svg" caption="Heat profile with sinusoidal boundary conditions" >}}

Again we see an increasing phase lag, where after a while the soil is even coldest in the summer and warmest in the winter, completely out of phase with the surface conditions. So can we solve this analytically? Could we be so lucky that an Ansatz with an exponential profile and linearly growing phase shift would work? Remarkably, yes! The heat equation $\dot{u}(t,x) = \alpha \, u''(t,x)$ on the half-line, with boundary condition $u(t,0) = A \sin\omega t$,
is solved by
\[ u(t,x) = A \exp\left(-\sqrt\frac{\omega}{2\alpha} x\right) \sin\left(\omega t - \sqrt\frac{\omega}{2\alpha} x \right) \]

Somewhat to my surprise, the heat equation can in fact sustain a traveling wave, though exponentially damped, with an attenuation length of $\sqrt{2\alpha/ \omega}$. Taking a [reasonable guess](https://www.cableizer.com/documentation/delta_soil/) of 0.5 mm²/s for the thermal diffusivity, that comes out to about 2¼ meters. That's a bit shorther than what I can infer from various real-world profiles found online, but it's at least in the right ballpark. Perhaps rainwater seeping in the soil accounts for part of it --- and although this could probably be adequately modeled by an advection term, I'm not going *that deep* for now.
