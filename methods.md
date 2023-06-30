SuperLite methods paper
=======================

[**Wagle et al. 2023, "Monte Carlo Radiation Transport for Astrophysical Transients Powered by Circumstellar Interation"**,](https://arxiv.org/abs/2305.17184)


Summary of SuperLite Physics
============================
SuperLite is a 1-D, multi-group, radiation transport code. It applies the methods of Implicit Monte Carlo (IMC)<sup>[1]</sup> and Discrete Diffusion Monte Carlo (DDMC)<sup>[2]</sup> to expanding spatial grids.  SuperLite is a heavily-modified version of its parent code SuperNu<sup>[3,4]</sup>. The physical opacity calculation includes elements from Hydrogen up to Zinc. SuperLite is motivated by the ongoing research into the effects of circumstellar interaction on observables of astrophysical transients: the brightness and shape of light curves and the temporal evolution of the spectra. SuperLite post-processes a single snapshot profile obtained from any hydrodynamic evolution code. SuperLite does not include any capabilities for hydrodynamic or time-dependent evolution.

[1] JA Fleck Jr, JD Cummings Jr, "An implicit Monte Carlo scheme for calculating
time and frequency dependent nonlinear radiation transport", (1971) <br>
[2] JD Densmore, TJ Urbatsch, TM Evans, MW Buksas, "A hybrid transport-diffusion
method for Monte Carlo radiative-transfer simulations", (2007) <br>
[3] RT Wollaeger, DR van Rossum, et al. "Radiation Transport for Explosive
Outflows: A Multigroup Hybrid Monte Carlo Method", (2013) <br>
[4] RT Wollaeger, DR van Rossum, "Radiation Transport for Explosive Outflows:
Opacity Regrouping", (2014)

Code Features
=============
The time-independent equations for photon transport and diffusion are solved
with Monte Carlo (MC) by discretizing the radiation energy in the system in the
form of MC packets (particles).  These particles are transported over the
computational domain (spatial grid) where they interact with the gas according
to multi-group opacities (groups).  Particles that leave the domain are tallied
per flux wavelength bin, and written to disk.

Particles
---
- MC Particles are generated in locations where energy sources are non-zero.
- The number of particles generated locally is determined by:
  - a power law of the local energy source,
  - the fraction of the local energy source to the total energy source,
  - a minimal number of particles per grid cell with non-zero energy source.
- Energy sources include:
  - volume sources and (optional/experimental) analytic boundary source
- Particles are stored in the particle array.
  - particle direction is stored in the lab frame,
  - particle energy/wavelength is stored in the comoving frame.

Transport
---
- Particle transport methods are:
  - Implicit Monte Carlo (IMC),
  - Discrete Diffusion Monte Carlo (DDMC).
- DDMC is used in optically thick regions of a domain.
- Geometries:
  - 1D spherical
- Assumptions:
  - local thermodynamic equilibrium (LTE), with optional non-LTE for hydrogen.
- particle transport is parallelized using a hybrid of MPI and OpenMP

Spatial grid
---
- The grid is the domain over which MC particles are tracked.
- The grid is spatial mesh with velocity bounds.
- The grid array is geometry-agnostic
- The grid array is compressed by not storing regions of zero opacity (zero mass).
- The grid array only stores quantities that are needed for the transport step

Gas
---
- Gas properties are domain decomposed.
- Gas properties include:
  - chemical composition,
  - density,
  - material temperature,
  - radiation temperature,
  - opacity: Thomson scattering, and multi-group absorption.
- Leakage (DDMC) and Planck opacities are calculated from scattering and absorption
  opacities.
- Mutli-group absorption opacity includes bound-bound (bb), bound-free (bf), and
  free-free (ff) data for Hydrogen to Zinc:
  - line data for bound-bound opacities are taken from
    http://kurucz.harvard.edu/atoms.html.

Groups
---
- Opacity frequency dependence is discretized with multi-group.
- The frequency-resolved opacities are averaged within each group.
- The code may use a constant multi-group grid in units wavelength (cm)

IO
---
- input is divided in two categories:
  - model specific input files are named input.* and (see the Input/ directory):
    - input.par: runtime parameters,
    - input.str: velocity-mass-temperature-composition structure on the computational domain.
  - model independent data files are named data.* (see the Data/ directory):
    - data.bf_verner: bound-free cross section data (Verner et al. 1996, ApJ 465, 487)
    - data.ff_sutherland: free-free gaunt factor data (Sutherland 1998, MNRAS 300, 321)
    - data.ion: atomic level data (from http://kurucz.harvard.edu/atoms.html)
    - Atoms/data.atom.* bound-bound transition data
    - Atoms/data.nlte.h1 bound-bound transition, radiative recombination/ionization,
      and collisional excitation data for hydrogen
- output files are named output.*.
  - stdout is written to output.log unless disabled in by an input parameter.
  - flux variables are saved as output.flx_*
  - grid variables are saved as output.grd_*
  - group variables are saved as output.grp_*
  - total (integrated over the domain) energy budget numbers are saved as output.tot_energy.

More information on IO can be found in the [Input Parameters](\inputpar.md), the [Input Structure](inputstr.md), and the [Output Files](\output.md) sections.


Coding standards
================

  - Source files with the .f suffix adhere to Fortran fixed format standard. The default
    indentation width is 1.
  - Source files with the .f90 suffix adhere to Fortran free format standard. The default
    indentation width is 3.
  - Each subroutine/function contains a comment section that describes its functionality.
  - All public variables in a module have a common 'xxx_' suffix that is unique to that
    module.
  - All variables, subroutines, functions, intrinsics, etc. have lowercase names, except
    for a USE Statement for modules that are used in the same file in which they are
    defined.
