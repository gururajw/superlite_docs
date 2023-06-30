Input Parameters
================

The input parameters for the model are provided through input.par file.
Following section lists all the parameters used in the code with their default values.
Additional description is provided for some of the important parameters is provided following
the list of all parameters.

The Input/ directory of the code contains an example input.par file.


**All parameters:**
-------------------

SuperLite runtime parameters are read-in from the input.par file. The inputparmod.f file defines the input parameters. As of 2023-06-09 the parameter definitions are as follows:

    c-- model id and comments
          character(40) :: in_name = "spn"  !simulation name/title, for post-processing identification
          character(80) :: in_comment = ""  !comment if any
    c-- parallelization
          integer :: in_nomp = 1  !number of openmp threads
    c
    c-- experimental iterative approach
          integer :: in_niter = 1 !number of iterations (currently, use 1.)
    c
    c-- grid geometry and dimensions
          integer :: in_grd_igeom = 11 !only 11 for 1D-sph is accepted for now
          integer :: in_ndim(3) = [1, 1, 1]  ![nx,1,1] nx: number of x-direction cells
    c
    c
    c-- group structure
          integer :: in_grp_ng = -1      !number of groups: 0 read wl-grid from file
          integer :: in_grp_ngs = 1      !number of subgroups per opacity group
          real*8 :: in_grp_wlmin =   100d-8  !lower wavelength boundary [cm]
          real*8 :: in_grp_wlmax = 32000d-8  !upper wavelength boundary [cm]
    c
    c
    c-- outbound flux group and direction bins
          integer :: in_flx_ndim(3) = [0, 1, 1] !number of output flux groups [n, 1, 1]: n=0 read flux-grid from file
          real*8 :: in_flx_wlmin =  1000d-8  !lower wavelength flux boundary [cm]
          real*8 :: in_flx_wlmax = 32000d-8  !upper wavelength flux boundary [cm]
    c
    c-- interior source
          integer :: in_src_ns = 0     !number of source particles generated per time step (total over all ranks)
          integer :: in_src_n2s = -1   !2^n source particles generated per time step (total over all ranks)
          real*8 :: in_srcepwr = 1d0  !source particle number-energy slope, 1 is linear, equal number of packets per erg.
          real*8 :: in_L_bol = 1d0 ![erg/s] !bolometric luminosity
    c-- boundary source
          real*8 :: in_L_inner = 0d0 ![erg/s] !experimental inner boundary source
          character(4) :: in_surfsrcmu = 'isot' !isot|beam: surface source direction distribution
          real*8 :: in_srcmax = 0d0 !peak source strength
    c
    c-- transport
          logical :: in_trn_errorfatal = .true. !stop on transport error, disable for production runs
          real*8 :: in_trn_tauddmc = 5d0  !number of mean free paths per cell required for DDMC
          real*8 :: in_taulump = 10d0 !number of of mean free paths needed to lump DDMC groups
          logical :: in_puretran = .false. !use IMC only instead of IMC+DDMC hybrid
          logical :: in_trn_noamp = .true.  !disable amplification factor
          logical :: in_thm_aniso = .false. !experimental anisotropic scattering
    c
    c-- debugging
          logical :: in_noeos = .false.     !don't use the EOS !debugging tool
    c
    c-- physical opacities
          real*8 :: in_opacmixrossel = 0d0 !mix rosseland with planck average, 1=pure rosseland
    c-- physical opacities debuging
          logical :: in_nobbopac = .false.  !turn off bound-bound opacity
          logical :: in_nobfopac = .false.  !turn off bound-bound opacity
          logical :: in_noffopac = .false.  !turn off bound-bound opacity
          logical :: in_nothmson = .false.  !turn off thomson scattering
    c
    c-- experimental nebular approximation
          logical :: in_nebular = .false. ! turn on nebular approximation (experimental feature)
    c-- photospheric radius for dilution factor
          real*8 :: in_R_phot = -1 ![cm] photospheric radius where Rosseland mean optical depth = 1
    c
    c-- NLTE treatment for hydrogen b-b opacity calculations
          logical :: in_nlte = .false. ! turn on NLTE
          integer :: in_nlte_nelem = 1 ! number of elements treated with nLTE ! for now keep =1 for h
    c
    c-- analytic opacities
          character(4) :: in_opacanaltype = 'none'    !none|grey|mono|line: group opacity structure type
    c-- line specific group structure
          real*8 :: in_ldisp1 = 1d0  !loosely speaking, the analytic odd group line strength
          real*8 :: in_ldisp2 = 1d0  !loosely speaking, the analytic even group line strength
    c-- scattering terms:
          real*8 :: in_gas_sigcoef = 0d0  !power law absorption opacity coefficient
          real*8 :: in_gas_sigtpwr = 0d0  !power law absorption opacity temperature exponent
          real*8 :: in_gas_sigrpwr = 0d0  !power law absorption opacity density exponent
    c-- absorption terms:
          real*8 :: in_gas_capcoef = 0d0  !power law absorption opacity coefficient
          real*8 :: in_gas_captpwr = 0d0  !power law absorption opacity temperature exponent
          real*8 :: in_gas_caprpwr = 0d0  !power law absorption opacity density exponent
    c
    c
    c-- output
          logical :: in_io_grabstdout = .false.  !write stdout to file
          logical :: in_io_dogrdtally = .false. !write transport tallies per grid cell
          logical :: in_io_nogriddump = .false.  !don't write grid cell variables
          character(4) :: in_io_opacdump = 'off '    !off|one|all: write opacity data to file
          character(4) :: in_io_pdensdump = 'off '   !off|on: write partial densities to file
          logical :: in_io_profdump = .false.  !write profile to file at the end
          logical :: in_io_debugging = .false. !sets nogriddump to false, opacdump,pdensdump,profdump,dogrdtally to true


Following text describes some of the important parameters.

        1. Source particles:

          The number of source particles needs to be a non-zero integer number. in_src_n2s defines the
          number of source particles as powers of 2. For example, in_src_n2s=10 will result in 1024
          particles. Alternately, in_src_ns defines the number of source particles. At least one of
          these two parameters must be set. If both are set, then the maximum of the two shall be used.
          Generally, about 10-20 source particles per cell per group per MPI rank are sufficient for
          better statistics and MC noise reduction. For example, if the grid consists of 100 cells and
          1000 group bins are set and 1 MPI rank is used, then 10x100x1000x1 = 1,000,000 (~2^20) source
          particles can be used.

          in_srcepwr changes the distribution of the source particles. When lowered from 1, it shall
          distribute more particles to the outer cells.

          in_L_bol can be set to a non-zero value to normalize the spectrum to this luminosity. If left
          to the default value of 1, then the output spectrum will be normalized to 1.

          There is an experimental implementation to use an inner boundary source. Setting in_L_inner
          to a non-zero value will enable this option. However, use this at your own discretion.
          in_srcmax sets the effective temperature for this inner boundary source to evaluate to
          wavelength distribution of source particles.

        2. Group bins:
          The lower and upper boundary of the wavelength grid, in_grp_wlmin, in_grp_wlmax and the number
          of group bins in_grp_ng for the opacity and MC transport calculations. The groups can be
          further subdivided in in_grp_ngs number of subgroups for opacity calculations. The lower and
          upper bounds of the simulated group bins can be important to dynamics of the photons, but can
          be not ultimately be of interest to the spectrum. The group bounds and number of bins depends
          on the problem at hand.

        3. Frequency bins:
          The frequency grid is used for the output spectrum. The lower and upper boundary of the
          wavelength grid in_flx_wlmin, in_flx_wlmax and the number of flux-grid cells in_flx_ndim(0)
          are usually chosen to be similar to the group-grid values above. If they are much coarser
          information will be lost, and if they are much finer only noise gets added. The viewing angle
          bins and the polar direction for in_flx_ndim are currently set to 1 for 1-D spherical geometry.
