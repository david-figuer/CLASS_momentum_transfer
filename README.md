# CLASS with Dark Energy–Dark Matter Elastic Interaction

This repository contains a **modified version of CLASS (Cosmic Linear Anisotropy Solving System)** implementing an **elastic momentum-transfer interaction between Dark Energy (DE) and Dark Matter (DM)** at the level of cosmological perturbations.

The model implemented in this code is based on a series of peer-reviewed publications and a doctoral thesis developed by the author of this repository (**David Figueruelo**) in collaboration with others (José Beltrán Jiménez, Dario Bettoni, and Florencia A. Teppa Pannia). **Any scientific use of this code requires citation of at least the first reference listed in the Citations section below.**

This implementation was originally developed starting from **CLASS v2.7.2**. The **current public version of this repository corresponds to CLASS v3.3.4**.

---

## Related Codes

An equivalent implementation of the DE–DM momentum-transfer model based on **CAMB** can be found at:

- [CAMB_DMDE_momentum_transfer](https://github.com/fateppapannia/CAMB_DMDE_momentum_transfer)

---

## Origin, Attribution, and Relation to CLASS

CLASS is originally developed by **Julien Lesgourgues**, **Thomas Tram**, and **Nils Schoeneberg**, with major contributions from many other authors.

Original CLASS repository:  
https://github.com/lesgourg/class_public

This repository is **NOT an official GitHub fork of CLASS**. It is an **independent repository containing a modified version of the CLASS code**. The original structure, philosophy, and licensing of CLASS are preserved.

As this code is a modified version of CLASS, users must also comply with the **citation requirements of the original CLASS code**. In particular, any scientific work using this code must cite at least the CLASS paper indicated in the original CLASS README:

**CLASS II: Approximation schemes**  
https://arxiv.org/abs/1104.2933

in addition to the model-specific references listed below.

---

## Structure of the Modifications

All modifications introduced in this repository are **explicitly delimited in the source code** by the markers:

```c
Modification_momentum_transfer{
    ... modified code ...
Modification_momentum_transfer}
```

No undocumented or hidden changes are present outside these blocks.

---

## Physical Model

This code implements an **elastic scattering (momentum transfer) interaction between Dark Energy and Dark Matter**, modifying the **velocity (θ) equations** of DE and DM at the level of **linear cosmological perturbations**.

---

## Dark Energy Description

Dark Energy is treated using the **fluid scheme**.  
The **PPF scheme must be disabled**.

Required `.ini` options:

```ini
use_ppf = no
Omega_Lambda = 0
gauge = newtonian
```

---

## Stability Considerations

- **w_DE > -1** → positive coupling, suppression effect present  
- **w_DE < -1** → negative coupling, stability ensured

---

## New Model Parameter

The coupling parameter **α\*** is implemented as:

```ini
alpha_model
```

Defined in:
- `input.c` (default `alpha_model = 0.0`)
- `background.h` (`double alpha_model;`)

---

## Modified Perturbation Equations

### Dark Matter Velocity

```c
dy[pv->index_pt_theta_cdm] =
  - a_prime_over_a * y[pv->index_pt_theta_cdm]
  + metric_euler
  + (pba->alpha_model * pow(a,4) * pba->H0 / pba->Omega0_cdm)
    * (y[pv->index_pt_theta_fld] - y[pv->index_pt_theta_cdm]);
```

### Dark Energy Velocity

```c
dy[pv->index_pt_theta_fld] =
  -(1 - 3*cs2) * a_prime_over_a * y[pv->index_pt_theta_fld]
  + cs2 * k2 / (1 + w_fld) * y[pv->index_pt_delta_fld]
  + metric_euler
  - (pba->alpha_model * pow(a,4 + 3*w_fld) * pba->H0
     / (pba->Omega0_fld * (1 + w_fld)))
    * (y[pv->index_pt_theta_fld] - y[pv->index_pt_theta_cdm]);
```

---

## Input Files

A reference input file **mymodel.ini** is provided.  
Required options must always be set.

---

## Compilation and Usage

```bash
make -j class
./class mymodel.ini
```

---

## Citations

1. https://inspirehep.net/literature/1849615  
2. https://inspirehep.net/literature/1869592  
3. https://inspirehep.net/literature/2156882  
4. https://inspirehep.net/literature/2615535  
5. https://inspirehep.net/literature/2765256  
6. https://inspirehep.net/literature/2842645  
7. Doctoral thesis: https://inspirehep.net/literature/2722259  

The model was first introduced in:

> M. Asghari, J. Beltrán Jiménez, S. Khosravi & D. F. Mota,  
> *On structure formation from a small-scales-interacting dark sector*,  
> arXiv:1902.05532.

---

## License

Inherits the license and citation policy of CLASS.

---

## Contact

**David Figueruelo**  
david.figueruelo@ehu.eus
