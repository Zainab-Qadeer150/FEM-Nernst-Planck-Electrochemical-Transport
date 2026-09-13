# FEM Nernst–Planck Electrochemical Transport

This project extends the verified two-dimensional FEM diffusion solver developed in a separate foundational repository. The present repository focuses on coupled ion transport, electrode boundary fluxes, electric-field-driven migration, pH evolution, and parameter-sensitivity analysis.

## Overview

This repository demonstrates the development of a two-dimensional electrochemical ion-transport model using the finite element method (FEM). The model includes transport and interaction of hydrogen ions $\mathrm{H}^{+}$ and hydroxide ions $\mathrm{OH}^{-}$ in a computational domain.

The framework includes:

* diffusion due to concentration gradients;
* migration due to applied electric field;
* localized ion injection at electrode interfaces;
hydrogen hydroxide neutralization;
time-dependent pH variation;
* checks of numerical conservation and positivity;
parameter sensitivity analysis

This project involves the extension of a previously-validated two-dimensional FEM diffusion solver to a more complex electrochemical transport problem.

---

## Project Objectives

The main objectives of this project are to:

1. Develop a coupled reaction–diffusion model for hydrogen and hydroxide ions;
2. Introduce localized electrode boundary fluxes;
3. Calculate time-dependent pH distributions;
4. Solve the electric-potential equation;
5. Incorporate ionic migration through the Nernst–Planck equation;
6. Verify concentration positivity and ion mass balance;
7. Investigate the effects of applied voltage, electrode flux, and reaction rate;
8. Establish a numerical framework for more realistic electrochemical simulations.

---

## Mathematical Model

### Electric Potential

The electric potential, $\phi$, is governed by

$$
\nabla\cdot\left(\sigma\nabla\phi\right)=0,
$$

where $\sigma$ is the electrical conductivity.

The electric field is calculated from

$$
\mathbf{E}=-\nabla\phi.
$$

A potential difference is applied between the two electrodes. The resulting electric field drives positively and negatively charged ions in opposite directions.

### Nernst–Planck Flux

The flux of ionic species $i$ is described by the Nernst–Planck equation [1,2]:

$$
\mathbf{J}_i
=
-D_i\nabla c_i
-
z_iD_i\frac{F}{RT}c_i\nabla\phi,
$$

where:

* $c_i$ is the concentration of species $i$;
* $D_i$ is its diffusion coefficient;
* $z_i$ is its ionic charge;
* $F$ is Faraday’s constant;
* $R$ is the universal gas constant;
* $T$ is the absolute temperature;
* $\phi$ is the electric potential.

The first term represents diffusion, while the second term represents electric-field-driven migration.

For hydrogen and hydroxide ions,

$$
z_{\mathrm{H}^{+}}=+1,
\qquad
z_{\mathrm{OH}^{-}}=-1.
$$

Therefore, the two ions migrate in opposite directions under the same electric field.

### Species-Conservation Equations

The concentration of each species satisfies

$$
\frac{\partial c_i}{\partial t}
=
-\nabla\cdot\mathbf{J}_i
-
R(c_{\mathrm{H}^{+}},c_{\mathrm{OH}^{-}}).
$$

The neutralization reaction is modelled as

$$
R(c_{\mathrm{H}^{+}},c_{\mathrm{OH}^{-}})
=
k\,c_{\mathrm{H}^{+}}c_{\mathrm{OH}^{-}},
$$

representing

$$
\mathrm{H}^{+}+\mathrm{OH}^{-}
\longrightarrow
\mathrm{H_2O}.
$$

Because the same reaction term is removed from both species, hydrogen and hydroxide ions are consumed in equal amounts.

### pH Calculation

The pH is calculated from the hydrogen-ion concentration:

$$
\mathrm{pH}
=
-\log_{10}\left(c_{\mathrm{H}^{+}}\right).
$$

To avoid taking the logarithm of zero, a safe numerical concentration is used:

$$
c_{\mathrm{safe}}
=
\max\left(c_{\mathrm{H}^{+}},10^{-14}\right).
$$

Therefore,

$$
\mathrm{pH}
=
-\log_{10}\left(c_{\mathrm{safe}}\right).
$$

---

## Numerical Method

The computational domain is discretized using linear triangular finite elements [3,4].

The standard FEM matrices are:

* the consistent mass matrix, $M$;
* the lumped mass matrix, $M_L$;
* the unit-diffusion stiffness matrix, $K_0$;
* the migration matrices associated with the electric field.

A mass-lumped formulation is used to improve concentration positivity near localized electrode sources.

Time integration is performed using a first-order implicit–explicit Euler method:

* diffusion and migration are treated implicitly;
* the nonlinear neutralization reaction is treated explicitly;
* electrode source terms are included through boundary load vectors.

The constant system matrices are factorized once using LU factorization and reused during time integration.

---

## Computational Configuration

| Parameter                       |                                           Value |
| ------------------------------- | ----------------------------------------------: |
| Domain                          |             $[0,1]\times[0,1]\ \mathrm{cm}^{2}$ |
| Number of nodes                 |                                             169 |
| Number of triangular elements   |                                             288 |
| Hydrogen diffusion coefficient  | $9.31\times10^{-5}\ \mathrm{cm}^{2}/\mathrm{s}$ |
| Hydroxide diffusion coefficient | $5.27\times10^{-5}\ \mathrm{cm}^{2}/\mathrm{s}$ |
| Baseline applied voltage        |                              $0.10\ \mathrm{V}$ |
| Baseline reaction-rate constant |                                          $50.0$ |
| Initial ion concentration       |              $1.0\times10^{-7}\ \mathrm{mol/L}$ |
| Initial time                    |                                 $0\ \mathrm{s}$ |
| Final time                      |                               $600\ \mathrm{s}$ |
| Time-step size                  |                              $0.25\ \mathrm{s}$ |
| Number of time steps            |                                            2400 |

> **Modelling note:** The selected voltage, electrode-flux, and reaction-rate values are effective numerical parameters used to investigate model behaviour. They are not presented as experimentally validated treatment conditions.

---

## Project Development

### Part 5: Coupled Reaction–Diffusion Model

The single-species diffusion solver is extended to hydrogen and hydroxide ions with different diffusion coefficients and a nonlinear neutralization reaction.

This part verifies:

* coupled transport of two chemical species;
* equal consumption by neutralization;
* concentration positivity;
* conservation of the hydrogen–hydroxide mass difference.

### Part 6: Electrode Fluxes and pH Evolution

Localized electrode boundary fluxes are introduced. The anode supplies hydrogen ions, while the cathode supplies hydroxide ions.

This part adds:

* physical ion concentrations;
* localized electrode source vectors;
* a lumped mass matrix;
* integrated ion mass balances;
* cumulative neutralization;
* time-dependent pH distributions.

### Part 7: Nernst–Planck Migration

The electric-potential equation and ionic migration are incorporated into the transport model.

This part demonstrates that:

* the electric potential satisfies its prescribed boundary values;
* the applied voltage produces an electric field;
* positive and negative ions migrate in opposite directions;
* migration changes the concentration and pH distributions;
* the discrete migration operator preserves global conservation.

### Part 8: Parameter-Sensitivity Analysis

A one-parameter-at-a-time sensitivity analysis is performed. Three parameters are tested at low, baseline, and high values:

1. applied electrode voltage;
2. electrode ion flux;
3. neutralization reaction-rate constant.

Only one parameter is changed during each sensitivity study, while all remaining parameters are kept at their baseline values.

The simulations are compared using:

* minimum final pH;
* maximum final pH;
* final integrated ion concentrations;
* cumulative neutralization;
* concentration positivity;
* ion mass-balance errors;
* final two-dimensional pH distributions.

---

## Key Results

For the baseline Nernst–Planck simulation with an applied voltage of $0.10\ \mathrm{V}$:

* the electric potential remained between $0$ and $0.10\ \mathrm{V}$;
* the maximum potential error was approximately $3.68\times10^{-16}$;
* the mean electric field was approximately $(0.10,0)\ \mathrm{V/cm}$;
* hydrogen and hydroxide ions migrated in opposite directions;
* the final integrated concentration of each ion was approximately $3.575\times10^{-3}$;
* the final pH ranged from approximately $1.544$ to $11.964$;
* all concentrations remained non-negative;
* the maximum mass-balance errors were of the order of $10^{-16}$.

These results confirm that the electric-potential, migration, electrode-source, reaction, and mass-balance terms were assembled consistently.

---

## Sensitivity-Analysis Interpretation

The parameter study provides the following numerical observations:

* Increasing the applied voltage strengthens ionic migration and changes the interaction between the hydrogen and hydroxide fronts;
* Increasing the electrode flux increases the amount of ions introduced into the domain and produces more extreme acidic and basic conditions
* Increasing the reaction-rate constant increases neutralization where both ionic species overlap;
* The spatial pH maps reveal changes that cannot be identified from integrated concentrations alone;
* Mass-balance and positivity checks confirm that the numerical solution remains reliable throughout the parameter studies.

The sensitivity analysis helps identify which parameters have the strongest influence on pH evolution and neutralization. It also provides guidance for future model calibration, experimental comparison, and electrochemical-system design.

---

## Numerical Verification

The implementation checks that:

* Every triangular element has a positive area;
* The total mesh area equals the domain area;
* The FEM matrices have the correct dimensions;
* The mass and stiffness matrices are symmetric;
* All computed concentrations and pH values are finite;
* No significant negative concentrations are produced;
* The electric potential satisfies the prescribed boundary conditions;
* Hydrogen and hydroxide migration directions are physically consistent;
* The migration operators preserve global mass;
* Electrode injection and neutralization satisfy the ion mass balances.

All verification checks passed for the reported simulations.

---

## Scope and Limitations

The present model provides a verified numerical framework rather than a complete experimentally calibrated electrochemical system.

The following effects are not yet included:

* Concentration-dependent electrical conductivity;
* Convection or fluid flow;
* Additional supporting electrolyte species;
* Poisson coupling based on local charge density;
* Full water-equilibrium chemistry;
* Activity-coefficient corrections;
* Butler–Volmer electrode kinetics;
* Experimentally calibrated reaction and electrode parameters;
* Patient-specific or tissue-specific geometry.

Therefore, the numerical results should be interpreted as model-based computational results rather than direct clinical or experimental predictions.

---

## Future Work

Future developments may include:

* additional ionic species;
* Poisson–Nernst–Planck coupling;
* Butler–Volmer electrode kinetics;
* concentration-dependent material properties;
* adaptive mesh refinement;
* higher-order time-integration methods;
* experimental parameter calibration;
* comparison with COMSOL Multiphysics;
* application to complex biological and electrochemical geometries.

---

## References

[1] COMSOL AB, “The Nernst–Planck Equations,” COMSOL Multiphysics Documentation.  
https://doc.comsol.com/6.3/doc/com.comsol.help.echem/echem_ug_electrochem.06.085.html

[2] S. C. Brenner and L. R. Scott, *The Mathematical Theory of Finite Element Methods*, 3rd ed., Springer, 2008.  
https://doi.org/10.1007/978-0-387-75934-0

[3] M. S. Gockenbach, *Understanding and Implementing the Finite Element Method*, SIAM, 2006.  
https://doi.org/10.1137/1.9780898717846

[4] D. M. Hamby, “A Review of Techniques for Parameter Sensitivity Analysis of Environmental Models,” *Environmental Monitoring and Assessment*, vol. 32, pp. 135–154, 1994.  
https://doi.org/10.1007/BF00547132

[5] A. Saltelli, M. Ratto, T. Andres, F. Campolongo, J. Cariboni, D. Gatelli, M. Saisana, and S. Tarantola, *Global Sensitivity Analysis: The Primer*, John Wiley & Sons, 2008.  
https://doi.org/10.1002/9780470725184

[6] J. Newman and K. E. Thomas-Alyea, *Electrochemical Systems*, 3rd ed., John Wiley & Sons, 2004.  
https://books.google.com/books?id=vArZu0HM-xYC

[7] P. Vanýsek, “Ionic Conductivity and Diffusion at Infinite Dilution,” in *CRC Handbook of Chemistry and Physics*, CRC Press.

---

## Author

**Zainab Qadeer**
BS Mathematics, University of Engineering and Technology Lahore
Research interests: finite element methods, numerical partial differential equations, mathematical modelling, scientific computing, and electrochemical transport.

---

## License

This project is released under the MIT License.
