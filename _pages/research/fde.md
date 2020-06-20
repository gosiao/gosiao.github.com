---
permalink: /research/fde
layout: archive
title: "Molecules in realistic environments"
author_profile: false
---

*Molecules in realistic environments* is a shortcut phrase coined for 
complex molecular systems 
which additionally can be subjected to external perturbations.
Examples of such systems, 
including for instance molecules in condensed phases or on interfaces 
which involve heavy elements (from the bottom of the periodic table) 
and are perturbed by probing electromagnetic fields in spectroscopic experiments, 
are ubiquitous in research conducted to predict 
and understand the properties and behavior of molecules 
in endless applications in various domains of biology and chemistry.
In addition, due to the presence of heavy elements in such systems,
many applications that appear in this context are of the strategic importance 
for the sustainable future of the modern society. 
Examples include applications in medicine (radiopharmaceuticals, nuclear imaging agents)
and various industries (catalysts, photonic devices, single molecular magnets),
energy sources (nuclear fuels), to name but a few.

The computational studies of such systems - which are the core of this project - are also
invaluable for basic research. They make significant contributions to understanding the
electronic structure and properties of complex molecular ensembles,
through the information retrieved
from the analysis of interactions between molecules and between molecules and external fields.
In turn, the knowledge of electronic structure and properties of molecules 
with heavy elements in complex environments is the first step in an efficient design 
of functional molecular materials and drugs. 
Among various applications, this project will consider systems which can be used as chemical sensors, 
molecule-based materials of desired magnetic properties or as radiopharmaceuticals.


The computational studies of such systems are not only run for application purposes,
but they also provide invaluable theoretical information, since through the
interactions between molecules and between molecules and external fields, 
the electronic structure and properties of such systems can be examined and better understood.


However, computer simulations of such ensembles are not easy.
The methodology required for this task should include the relativistic effects (scalar and spin-orbit), 
have a good description of electron correlation and, finally, 
should account for the presence of an environment, which may significantly affect 
the geometry, electronic structure and properties of the molecule of interest.
More advanced quantum chemistry methods which are able to encompass the relativistic 
and electron correlation effects are computationally very expensive, 
therefore cannot be used to a molecule and its environment treated as whole system. 

A promising alternative is to use embedding methods, 
in which the whole system is divided into subsystems: 
an *active* molecule of interest and its *environment*, 
treated separately by the best (and tailored for them) quantum chemistry models. 

A promising example of such method is the frozen density embedding (FDE), 
in which this partitioning is performed in terms of the electron density 
and the effect of all other subsystems on the molecule for which 
the electronic structure and properties are sought is accounted for through 
the so-called embedding potential. 
FDE is based on the density functional theory (DFT), 
therefore the embedding potential is defined analogously to 
exchange-correlation potential in Kohn-Sham DFT, 
nevertheless all the subsystems can either be described with DFT 
or with wave-function theory (WFT) based methods, 
what results in various embedding schemes, termed DFT-in-DFT, WFT-in-DFT, WFT-in-WFT.

Currently available FDE schemes are limited to non-relativistic or quasi-relativistic Hamiltonians 
and only to few molecular properties (not higher than of second-order). 
And the developments in the *fully*-relativistic four-component (4c) 
framework with the Dirac-Coulomb (DC) Hamiltonian and various DFT and WFT methods 
for the wide range of second- and third-order molecular properties are certainly needed.

This extension is essential to systems with heavy atoms and to properties which depend on the 
electron density near the nucleus - as for them the relativistic effects are expected to be 
significant - such as the properties observed in nuclear magnetic resonance (NMR) spectroscopy 
or in the X-ray spectroscopy, however owing to recent advancements in relativistic quantum chemistry, 
more unexpected examples of significant relativistic effects on valence properties are found.

The methodology development in this direction will therefore allow to revisit and calculate various properties, 
notably in systems with heavy elements, with the relativistic, correlation and environmental effects calculated from the start.





<br>


*Project funded by the [National Science Center](https://ncn.gov.pl/?language=en), grant number 2016/23/D/ST4/03217*

