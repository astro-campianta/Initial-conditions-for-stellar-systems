# Initial-conditions-for-stellar-systems

Abstract
This code generates initial conditions for stellar systems by extracting fundamental physical parameters from the corresponding distribution fuctions. Two different binary-creation workflows are supported:
\begin{itemize}
\item random pairing: draw all component masses independently from the IMF and then group them into singles and binary pairs;
\item q-distribution pairing: draw only single primary masses from the IMF and generate secondaries by sampling a mass-ratio from the selected q-distribution.
The generator is intended for research use (population synthesis initial conditions, N-body simulations, statistical testing), and is written to be configurable, easily reproducible and compatible with N-body simulators (e.g., Nbody 6++, Nbody 7 and PeTar).
