CLASS with Dark Energy–Dark Matter Elastic Interaction

This repository contains a modified version of CLASS (Cosmic Linear Anisotropy Solving System) implementing an elastic momentum-transfer interaction between Dark Energy (DE) and Dark Matter (DM) at the level of cosmological perturbations.

The model implemented in this code is based on a series of peer-reviewed publications and a doctoral thesis developed by the author of this repository. Any scientific use of this code requires citation of at least the first reference listed in the Citations section below.

This version corresponds to version 1.0 of the CLASS DE–DM elastic interaction model, developed starting from CLASS v2.7.2.

---------------------------------------------------------------------

Origin and attribution

CLASS is originally developed by:
- Julien Lesgourgues
- Thomas Tram
- Nils Schoeneberg

with major contributions from many other authors.

Original CLASS repository:
https://github.com/lesgourg/class_public

This repository is a scientific fork of CLASS.
The original structure, philosophy, and licensing of CLASS are preserved.

---------------------------------------------------------------------

Structure of the modifications

All modifications introduced in this repository are explicitly delimited in the source code by the markers:

Modification_momentum_transfer{
    ... modified code ...
Modification_momentum_transfer}

This convention allows the user to:
- easily identify all changes with respect to vanilla CLASS,
- track the physical implementation of the DE–DM interaction,
- distinguish original CLASS code from model-specific additions.

No undocumented or hidden changes are present outside these blocks.

---------------------------------------------------------------------

Physical model

This code implements an elastic scattering (momentum transfer) interaction between Dark Energy and Dark Matter, following the theoretical framework developed in the references listed in the Citations section.

The interaction modifies the velocity (theta) equations of DE and DM at the level of linear perturbations.

---------------------------------------------------------------------

Dark Energy description

Dark Energy is treated using the fluid scheme.
The PPF scheme must be disabled.

To correctly activate the model, the input .ini file must contain:

use_ppf = no
Omega_Lambda = 0
gauge = newtonian

---------------------------------------------------------------------

Stability considerations

For w_DE > -1:
- the coupling constant must be positive to avoid instabilities,
- a suppression effect appears.

For w_DE < -1:
- the coupling constant must be negative to ensure stability,
- no suppression effect appears.

---------------------------------------------------------------------

New model parameter

The coupling parameter denoted as alpha* in the reference papers is implemented in the code as:

alpha_model

Definition in the code:

- input.c
  Declared as a new input parameter and assigned through class_read_double.
  Default value: alpha_model = 0.0

- background.h
  Declared as:
  double alpha_model;

---------------------------------------------------------------------

Modified perturbation equations

The interaction is implemented in the Newtonian gauge by modifying the perturbation equations in perturbations.c.

Dark Matter velocity equation

Original CLASS equation:

dy[pv->index_pt_theta_cdm] =
  - a_prime_over_a * y[pv->index_pt_theta_cdm]
  + metric_euler;

Modified equation:

dy[pv->index_pt_theta_cdm] =
  - a_prime_over_a * y[pv->index_pt_theta_cdm]
  + metric_euler
  + (pba->alpha_model * pow(a,4) * pba->H0 / pba->Omega0_cdm)
    * (y[pv->index_pt_theta_fld] - y[pv->index_pt_theta_cdm]);

Corresponding coupling term:

C.T = (alpha* · H0 · a^4 / Omega_DM) · (theta_DE − theta_DM)

---------------------------------------------------------------------

Dark Energy velocity equation

Original CLASS equation:

dy[pv->index_pt_theta_fld] =
  -(1 - 3*cs2) * a_prime_over_a * y[pv->index_pt_theta_fld]
  + cs2 * k2 / (1 + w_fld) * y[pv->index_pt_delta_fld]
  + metric_euler;

Modified equation:

dy[pv->index_pt_theta_fld] =
  -(1 - 3*cs2) * a_prime_over_a * y[pv->index_pt_theta_fld]
  + cs2 * k2 / (1 + w_fld) * y[pv->index_pt_delta_fld]
  + metric_euler
  - (pba->alpha_model * pow(a,4 + 3*w_fld) * pba->H0
     / (pba->Omega0_fld * (1 + w_fld)))
    * (y[pv->index_pt_theta_fld] - y[pv->index_pt_theta_cdm]);

Corresponding coupling term:

C.T = − (alpha* · H0 · a^(4 + 3 w_DE) / (Omega_DE · (1 + w_DE)))
      · (theta_DE − theta_DM)

---------------------------------------------------------------------

Input files

A reference input file mymodel.ini is provided and plays the same role as explanatory.ini in vanilla CLASS.

- It contains all available options for this modified version.
- Users are encouraged to create smaller .ini files including only the needed parameters.
- The parameters use_ppf = no, Omega_Lambda = 0, and gauge = newtonian must always be set.

---------------------------------------------------------------------

Compilation and usage

Compile as standard CLASS:

make -j class

Run with:

./class mymodel.ini

To compile the Python wrapper:

make -j

---------------------------------------------------------------------

Citations (mandatory)

If you use this code in scientific work, you must cite at least the first reference below.
Additional citations are strongly encouraged when relevant.

1. https://inspirehep.net/literature/1849615
2. https://inspirehep.net/literature/1869592
3. https://inspirehep.net/literature/2156882
4. https://inspirehep.net/literature/2615535
5. https://inspirehep.net/literature/2765256
6. https://inspirehep.net/literature/2842645
7. Doctoral thesis (partial): https://inspirehep.net/literature/2722259

---------------------------------------------------------------------

License

This code inherits the same license as the original CLASS distribution.
Users must comply with the CLASS license and citation policy.

---------------------------------------------------------------------

Contact

Questions, doubts, or comments regarding this implementation are welcome.

David Figuer
davidfiguer@usal.es
