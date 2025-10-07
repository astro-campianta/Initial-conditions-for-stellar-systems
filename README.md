# Initial-conditions-for-stellar-systems

**Abstract**

This code generates initial conditions for stellar systems by extracting fundamental physical parameters from the corresponding distribution functions. Two different binary-creation workflows are supported:
* **random pairing**: draws all component masses independently from the IMF and then groups them into singles and binary pairs;
* **q-distribution pairing**: draws only single primary masses from the IMF and generates secondaries by sampling a mass-ratio from the selected q-distribution.
The generator is intended for research use (population synthesis initial conditions, N-body simulations, statistical testing), and is written to be configurable, easily reproducible and compatible with N-body simulators (e.g., Nbody 6++, Nbody 7 and PeTar).

**Code structure**

The code is divided into the following classes, each one including the possibility of plotting the sampled distribution functions:

* **StellarIMF**: generates masses ($M_\odot$) according to the Kroupa or the Salpeter IMF, in the selected mass interval;
* **BinaryCoupling**: pairs binary components by randomly associating their masses or using a mass-ratio extracted from either a uniform or a power-law q-distribution. It additionally assigns masses to single stars from the leftover mass sample and calculates the total mass of the system;
* **PhaseModel**: generates positions (pc) and velocities (km/s) of single stars and binary centers of mass according to either the Plummer or the King model;
* **SemiMajorAxis**: extracts semimajor axes (AU) for binary orbits from a uniform, a log-flat or a power-law distribution;
* **Eccentricity**: extracts eccentricities for binary orbits from a uniform, a Rayleigh, a thermal or a beta distribution;
* **SingleBinaryStars**: distinguishes between single and binary components positions (pc) and velocities (km/s), configuring each binary system according to its orbital parameters.

It furthermore contains the following functions:

* **fractality**: optionally modifies the spatial distribution of stars by introducing fractality;
* **virial_ratio**: calculates the virial ratio of the system and corrects stellar velocities to match the imposed equilibrium value;
* **main_random_pairing**: initializes classes and functions for the random pairing binary-creation workflow;
* **main_q_distribution**: initializes classes and functions for the q-distribution binary-creation workflow.

Each main function generates a dataframe listing stellar masses, position and velocity components by column, so that each raw entirely defines a single object. The dataframe is stored into a file .dat in the specified directory.
The code workflow can be selected by choosing either mode 1 (random pairing) or mode 2 (q-distribution) in the final cell.

**Parameters**

* **N_s**: number of single stars;
* **N_b**: number of binary systems;
* **N**: number of single stars and binary systems;
* **N_tot**: total number of stars, including single stars and binary components;
* **f_b**: binary fraction;
* **m_s**: masses of single stars;
* **m_1**: masses of primary binary components;
* **m_2**: masses of secondary binary components;
* **m**: masses of all stars, including single stars and binary components;
* **M**: total mass of the system;
* **X**: vector positions of all stars, including single stars and binary systems;
* **X_s**: vector positions of single stars;
* **X_1**: vector positions of primary binary components;
* **X_2**: vector positions of secondary binary components;
* **V**: vector velocities of all stars, including single stars and binary systems;
* **V_s**: vector velocities of single stars;
* **V_1**: vector velocities of primary binary components;
* **V_2**: vector velocities of secondary binary components;
* **q**: mass ratios;
* **a**: semi-major axes;
* **e**: eccentricities;
* **Q**: virial ratio;
* **W0**: dimensionless central potential (King model);
* **r_max**: maximum radius for ODE integration (King model);
* **D**: fractal dimension;
* **c**: concentration (scale) parameter. 

**Formulas**

* Salpeter IMF:
  ```math
  f(m) = C \, m^{-\alpha}; \quad 
  C = \frac{1-\alpha}{m_\text{max}^{1-\alpha} - m_\text{min}^{1-\alpha}}; \quad
  m(u) = \left[ m_\text{min}^{1-\alpha} + u \left( m_\text{max}^{1-\alpha} - m_\text{min}^{1-\alpha} \right) \right]^{\frac{1}{1-\alpha}}, \quad u \in [0,1]
* Kroupa IMF:
  ```math
  f(m) \propto
  \begin{cases} 
  m^{-\alpha_0}, & m_\text{min} \le m < m_1 \\
  m^{-\alpha_1}, & m_1 \le m < m_2 \\
  m^{-\alpha_2}, & m_2 \le m \le m_\text{max}
  \end{cases}; \quad
  \begin{aligned}
  C_1 &= m_1^{\alpha_1 - \alpha_0} \\
  C_2 &= C_1 \, m_2^{\alpha_2 - \alpha_1};
  \end{aligned} \quad
  \begin{aligned}
  A_1 &= \frac{m_1^{1-\alpha_0} - m_\text{min}^{1-\alpha_0}}{1-\alpha_0} \\
  A_2 &= C_1 \frac{m_2^{1-\alpha_1} - m_1^{1-\alpha_1}}{1-\alpha_1} \\
  A_3 &= C_2 \frac{m_\text{max}^{1-\alpha_2} - m_2^{1-\alpha_2}}{1-\alpha_2} \\
  A &= A_1 + A_2 + A_3
  \end{aligned}; \quad
  m(u) =
  \begin{cases}
  \left[ (1-\alpha_0) A u + m_\text{min}^{1-\alpha_0} \right]^{\frac{1}{1-\alpha_0}}, & 0 \le A u < A_1 \\
  \left[ (1-\alpha_1)\frac{A u - A_1}{C_1} + m_1^{1-\alpha_1} \right]^{\frac{1}{1-\alpha_1}}, & A_1 \le A u < A_1 + A_2 \\
  \left[ (1-\alpha_2)\frac{A u - A_1 - A_2}{C_2} + m_2^{1-\alpha_2} \right]^{\frac{1}{1-\alpha_2}}, & A_1 + A_2 \le A u \le A
  \end{cases}
* Uniform mass-ratio distribution:
  ```math
  f(q) = \frac{1}{q_\text{max} - q_\text{min}}; \quad 
  q(u) = q_\text{min} + u \,(q_\text{max} - q_\text{min}), \quad u \in [0,1]
* Power-law mass-ratio distribution:
  ```math
  f(q) = \frac{(\beta+1)\,q^{\beta}} {q_\text{max}^{\beta+1} - q_\text{min}^{\beta+1}}; \quad
  q(u) = \left[\,u \,\big(q_\text{max}^{\beta+1} - q_\text{min}^{\beta+1}\big) + q_\text{min}^{\beta+1}\right]^{\tfrac{1}{\beta+1}}, \quad u \in [0,1]
* Plummer model:
  ```math
  \rho(r) = \frac{3M}{4 \pi a^3} \left(1 + \frac{r^2}{a^2}\right)^{-\tfrac{5}{2}}; \quad
  r(u) = \frac{a}{\sqrt{u^{-\tfrac{2}{3}} - 1}}, \quad u \in [0,1]; \quad
  f(x) \propto x^2 \,(1 - x^2)^{\tfrac{7}{2}} \implies x = \frac{v}{v_e(r)} = v \sqrt{\frac{\sqrt{r^2 + a^2}}{2GM}}, \quad 0 \leq x \leq 1
* King model:
  ```math
  \rho(\psi) \propto e^{\psi}\,\mathrm{erf}(\sqrt{\psi}) - \sqrt{\tfrac{4\psi}{\pi}} \left( 1 + \tfrac{2}{3}\psi \right); \quad
  f(E) \propto 
  \begin{cases}
  e^E - 1, & E > 0 \\
  0, & E \leq 0
  \end{cases}; \quad
  E = \psi(r) - \tfrac{1}{2} v^2
* Uniform semi-major axis distribution:
  ```math
  f(a) = \frac{1}{a_\text{max} - a_\text{min}}; \quad a(u) = a_\text{min} + u \,(a_\text{max} - a_\text{min}), \quad u \in [0,1]
* Log-flat semi-major axis distribution (Öpik's law):
  ```math
  f(a) = \frac{1}{a \,\ln\!\left(\tfrac{a_\text{max}}{a_\text{min}}\right)}; \quad a(u) = a_\text{min} \left(\frac{a_\text{max}}{a_\text{min}}\right)^u, \quad u \in [0,1]
* Power-law semi-major axis distribution:
  ```math
  f(a) = 
  \begin{cases}
  \dfrac{(1-\beta)\,a^{-\beta}}{a_\text{max}^{1-\beta} - a_\text{min}^{1-\beta}}, & \beta \neq 1 \\[1em]
  \dfrac{1}{a \,\ln\!\left(\tfrac{a_\text{max}}{a_\text{min}}\right)}, & \beta = 1
  \end{cases}; \quad
  a(u) = 
  \begin{cases}
  \Big[\,u\,(a_\text{max}^{1-\beta} - a_\text{min}^{1-\beta}) + a_\text{min}^{1-\beta}\,\Big]^{\tfrac{1}{1-\beta}}, & \beta \neq 1 \\[1em]
  a_\text{min}\,\Big(\tfrac{a_\text{max}}{a_\text{min}}\Big)^u, & \beta = 1
  \end{cases},
  \quad u \in [0,1]
* Uniform eccentricity distribution:
  ```math
  f(e) = \frac{1}{e_\text{max} - e_\text{min}}; \quad e(u) = e_\text{min} + u \,(e_\text{max} - e_\text{min}), \quad u \in [0,1]
* Rayleigh eccentricity distribution:
  ```math
  f(e) = \frac{e}{\sigma^2} \exp\!\left(-\frac{e^2}{2\sigma^2}\right), \quad e \geq 0; \quad e(u) = \sigma \sqrt{-2 \ln(1-u)}, \quad u \in [0,1]
* Thermal eccentricity distribution:
  ```math
  f(e) = \frac{2e}{e_\text{max}^2 - e_\text{min}^2}; \quad e(u) = \sqrt{u \,(e_\text{max}^2 - e_\text{min}^2) + e_\text{min}^2}, \quad u \in [0,1]
* Beta eccentricity distribution:
  ```math
  f(e) = \frac{1}{B(\alpha,\beta)}\left(\frac{e - e_\text{min}}{e_\text{max} - e_\text{min}}\right)^{\alpha - 1}\left(1 - \frac{e - e_\text{min}}{e_\text{max} - e_\text{min}}\right)^{\beta - 1}\frac{1}{e_\text{max} -    e_\text{min}}; \quad e(u) = e_\text{min} + (e_\text{max} - e_\text{min}) \cdot x, \quad x \sim \mathrm{B}(\alpha,\beta), \quad u \in [0,1]

**Installation and usage**

Clone the repository:
```bash
git clone https://github.com/your-username/stellar-systems-simulator.git
cd stellar-systems-simulator
```
Create and activate a virtual environment:
* Linux / macOS:
  ```bash
  python3 -m venv venv
  source venv/bin/activate
  ```
* Windows:
  ```powershell
  python -m venv venv
  venv\Scripts\Activate
  ```
Install the dependencies:
```bash
pip install numpy pandas matplotlib scipy
```
Start Jupyter Notebook:
```bash
jupyter notebook
```
From the Jupyter interface, open **initial_conditions_generator.ipynb**. Run the cells sequentially to generate stellar systems with your chosen initial conditions (mode 1 or mode 2).

**Aknowledgements**

This Python code was developed in collaboration with Dr. Long Wang and is based on methods described in Wang et al. (2020, https://arxiv.org/abs/2006.16560). 



















  






