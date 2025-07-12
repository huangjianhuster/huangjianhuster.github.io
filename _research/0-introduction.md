---
title: "Studying Molecular mechanisms of protein functions using MD simulations (introduction)"
collection: research
category: projects
---

Protein function is encoded in their dynamic motions where protein can transit between different conformational states.

Those dynamic transitions are often coupled with other cellular components, such as ligands, lipids, partner proteins and other environmental factors. Those factors can effectively shape how proteins navigate through their conformational landscape and thus affect protein functions.

From the experimental side, single molecule measurement indeed can provide equilibrium distributions of observables of protein ensembles. Sophisticated cryo-EM can manage to capture multiple conformational states of proteins, building the high resolution structural basis for understanding underlying molecular mechanisms of protein function. However, both are time- and resource-consuming and are not easily scaleable, especially when considering complex perturbations from the native environment of protein in cells.

Molecular dynamics (MD) is a universal and powerful technique allowing effective sampling dynamic protein structural conformations and transitions in the all-atom resolution. By integrating on a pre-defined and parametrized potential energy function (or so-called force field) using Newtonian equations, MD provides a molecular level "movie" with all-atom and femtosecond spatial and temporal resolution within which every molecular motion is meticulously "recorded". Every frame in the "movie" will be one possible conformation of a protein. And by playing and rewinding the movie, we can see how the protein is "walking" along its conformational landscape.

I guess there is no need to explain more about Newtonian equations here, right? Those are just simply middle school physics. But to refresh your memory:

The parametrized potential energy function is defined from the fore field. $x$ is the coordinates of the protein system.

$$
E_{potential}(x)
$$

Force is calculated fro each atom.

$$
F_{i} = - {\partial E_{potential}}/{\partial x_{i}}
$$

Acceleration:

$$
a_{i} = \frac{F_{i}}{m_{i}}
$$

Numerical integration to calculate or update velocity and position:

$$
v_{i} (t + dt) = v_{i} (t) +a_{i} dt
$$

$$
x_{i} (t+dt) = x_{i} (t) + v_{i} dt
$$

There are actually some complications here. For example, the $dt$ should be very small since doing the numerical integration assumes the $a$ (acceleration) remain constant in the $dt$ time. But this is almost never true as all atoms are moving all at once and changing forces and acceleration for even an infinitesimal time. For accurate integrating the Newtonian equations, it requires more algorithmic development and error estimation which deserves a whole chapter to introduce and thus will not be covered here. For now, let's say, "magically" or by using highly advanced and accurate integration algorithm, we can have a perfect and error-less MD sampling of protein systems.

If given that we can sample infinite amount of time for a protein, the physics and math underlying MD will guarantee us to reach **ergodicity**, meaning not only every possible conformation has been sample but also their probability densities have been fully sampled and converged. The topic of why the physics and math of MD can ensure ergodicity involves a whole lot of explanations and thus will not be thoroughly discussed here. For now, we are just gonna accept this concept as a sort of a given. However, please realize that in really this is almost impossible to reach, and we need to assess the convergence (or ergodicity) of our simulations to ensure adequate coverage of the conformational landscape.

Now it is fascinating that our perfect MD simulations (errorless and with infinite amount of integration time) eventually give us the conformational samplings and all the important transitions dictating protein functions. Those dynamics ("movies") allow us to extract macroscopic observables for experimental testing, make predictions and generate further hypothesis. This has proven to be extremely useful for molecular mechanism studies as well as designing molecules possibly exerting certain perturbations.

There are caveats and limitations, however, for MD to sample protein conformations. As you are already aware of during the above discussion of the principle of MD, MD simulations will:
- inevitably have integration errors of course (though now some advanced algorithm can ensure symplectic, meaning error is somewhat conserved and will not scale up as simulation time grows)
- difficult to sample all possible states or difficult to ensure ergodicity. Thus convergence has to be thoroughly assessed before making concrete conclusions. However, it does not mean the insufficient sampling is useless. If relative probabilities of important states have been sampled and converged or timescale for quickly converged macroscopic observables has been reached, we can still get reasonable estimations of thermodynamic properties. 
- inevitably have systematic errors from the inaccuracies of the force field. I deliberately did not talk about the force field previously, again because it is another complicated topic and people now are still developing more accurate force fields. In principle, a force field is basically a mathematic function that accounts for all inter-atomic interactions. As you may already imagine, this is intrinsically hard and limited by current understanding of quantum mechanics for those inter-atomic forces. I will not go deeper to discuss force field development here but encourage readers to read reviews for this matter (it is also very interesting too! and imagine how fascinating it would be if one day we  have an universal force field for accurately presenting every chemical matter, biological and materials systems).



