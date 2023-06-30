Output Files
============

The output files are named __output.\*__. The stdout is written to **output.log** file, unless disabled by the input parameter `in_io_grabstdout`. The other output files can be roughly divided into three groups based on the variables - the spatial grid (**output.grd_**), the flux grid (**output.flx_**), the group grid (**output.grp_**).

The following text describes the output files in detail.

Flux grid
---------

SuperLite writes flux output in ascii. The number of columns equals the number of wl-bins.

- output.flx_grid: The wavelength grid [in_flx_ndim(1)].
- output.flx_luminos: A sequence of spectra, one spectrum per line. The values are in luminosity units [erg/s] and normalized to in_L_bol.
- output.flx_lumnum: The number of MC packets per flux bin.
- output.flx_lumdev: The standard deviation in the fluxes.

Spatial grid
------------

The grid variables can be mapped to spatial cells (with an (i,j,k) index) with
output.grd_grid.

As headers, output.grd_grid has (in row order) the geometry index (grd_igeom),
the number of spatial cells along each dimension (grd_nx,grd_ny,grd_nz),
and the total number of array cells used to yield an optimal row size (ncpr),
column size (nrow) to minimize padding cells per write
(the final header has nrow*ncpr,nrow,ncpr).

The next 3 rows are space or velocity values at the cell edges in the x,y,z
dimensions.

The following integers are the cell indices of grid variables for spatial index
locations (i,j,k), where the column index is the x-dimension (i), and the row
index is the serialized index for the y (j) and z (k) dimensions (the row index
is j+(k-1)*ny). Thus, the cell padding information can be used to remove extra
padding values from the grid output, and the grid index mapping to the (i,j,k)
spatial cell index can used to reconstruct spatial profiles of the
padding-stripped grid data.

Using parameter `in_io_nogriddump`, SuperLite optionally writes grid-based variables in ascii (output.grd_*). Grid variables include
- output.grd_nvol: number of MC particles instantiated.
- output.grd_temp: material temperature.
- output.grd_radtemp: radiation temperature.
- output.grd_eraddens: radiation energy density.

In addition using parameter `in_io_opacdump`, SuperLite optionally writes
- output.grd_capgrey: Planck opacity
- output.grd_capross: Rosseland mean opacity.
- output.grd_sig: scattering opacity.
- output.grd_capemitgrey: "emission" opacity relevant for non-LTE calcualtions.

Group grid
----------

Using parameter `in_io_opacdump`, SuperLite writes opacity/emissivity values per group per cell in ascii (output.grp_*).  So the number of columns equals the number of grp-bins and number of rows equals the number of spatial cells.

- output.grp_cap: Multi-group opacity.
- output.grp_emiss: Multi-group emissivity.
- output.grp_capemit: Multi-group "emission" opacity relevant for non-LTE calculations.

Miscellaneous
-------------

SuperLite writes several output files for monitoring the performance of the code including
- output.timing: runtime timers for different parts of the code.
- output.counters: number of MC particles created, transported, flux output, count of MC particles' IMC and DDMC steps, maximum number of steps, and method swaps.
- output.tot_energy: total (integrated over the domain) energy budget numbers.


Using parameter `in_io_profdump`, SuperLite optionally writes the **output.profile** file that contains following columns:
- Left and right bounds of spatial mesh.
- Left and right bounds of velocity coordinates corresponding to the spatial mesh.
- Mass, density, volume, material temperature, (estimated) radiation temperature.
- Calculated values of electron fraction (Ye), electron density (n_e), and number density of atoms (n_atom).
- Mass fractions as provided in the input.str file.

Using parameter `in_io_pdensdump`, SuperLite optionally writes **output.pdens** file that has partial densities for each ion of input elements and electron density per grid cell calculated in the opacity routine. This file is useful for debugging purpose and it works best in serial mode. (If MPI is enabled, then different ranks may access the file simultaneously, jumbling the output.)
