# Initial-conditions-for-stellar-systems

Abstract

This code generates initial conditions for stellar systems by extracting fundamental physical parameters from the corresponding distribution fuctions. Two different binary-creation workflows are supported:
* random pairing: draws all component masses independently from the IMF and then groups them into singles and binary pairs;
* q-distribution pairing: draws only single primary masses from the IMF and generates secondaries by sampling a mass-ratio from the selected q-distribution.
The generator is intended for research use (population synthesis initial conditions, N-body simulations, statistical testing), and is written to be configurable, easily reproducible and compatible with N-body simulators (e.g., Nbody 6++, Nbody 7 and PeTar).

Code structure

The code is divided into the following classes, each one including the possibility of plotting the sampled distribution functions:

* StellarIMF: generates masses (M$_\odot$) according to the Kroupa or the Salpeter IMF, in the selected mass interval;
* BinaryCoupling: pairs binary components by randomly associating their masses or using a mass-ratio extracted from either a uniform or a power-law q-distribution. It additionally assigns masses to single stars from the leftover mass sample and calculetes the total mass of the system;
* PhaseModel: generates positions and velocities of single stars and binary centers of mass according to either the Plummer or the King model;
* SemiMajorAxis: extracts semimajor axes in AU for binary orbits from a uniform, a log-flat or a power-law distribution;
* Eccentricity: extracts eccentricities for binary orbits from a uniform, a Rayleigh, a thermal or a beta distribution;
* SingleBinaryStars: distinguishes between single and binary components positions and velocities, configuring each binary system according to its orbital parameters.

It furthermore contains the following functions:

* fractality: optionally modifies the spatial distribution of stars by introducing fractality;
* virial_ratio: calculates the virial ratio of the system and corrects stellar velocities to match the imposed equilibrium value;
* main_random_pairing: initializes classes and functions for the random pairing binary-creation workflow;
* main_q_distribution: initializes classes and functions for the q-distribution binary-creation workflow.

Each main function generates a dataframe listing stellar masses, position and velocity components by column, so that each raw entirely defines a single object. The dataframe is stored into a file .dat in the specified directory.
The code worflow can be selected by choosing either mode 1 (random pairing) or mode 2 (q-distribution) in the final cell. 



