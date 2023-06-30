# Input Structure

This file contains the stratification information of the outflow including the physical
quantities such as mass, volume, temperature, abundances, etc. The first three lines
in the file contain the header and the rest of the file contains the profile data. The file
body consists of one row for each grid cell and one column for each variable.

The first line contains comments and it is ignored by the code while reading the file.
This line can be used to store information about the model like geometry, name, author,
simulation id, simulation date, etc. For example,

    # 1D spherical, w7 model

The second line contains the number of cells (rows), total number columns, and number of columns
that contain chemical abundances (mass fractions) of the elements.

    #      nx  ny  nz  ncol  nabund

The third line contains the headers of the columns, for example,

    #   x_left    x_right    vx_left    vx_right    mass    rad_temp    temp     h    he    fe    ni

The order of the variables, their column heads, and the description of each variable are as follows:

First 4 columns contain the position and velocity coordinates of the cells.

**Cell position (radial coordinate):** first two columns. The first column `x_left` defines the inner boundary, while the second column `x_right` defines the outer boundary of the cell.

*Notes:*
1. The radial coordinates shall be monotonically increasing. If not, the code will stop with an error message.

2. One could provide just one column for radial coordinates, in which case, they will be assumed to be the right boundaries of each cell, and the innermost boundary of the cell will be set to 0. Caution is advised that the innermost cell will be a sphere centered at 0, not a shell, and the physical quantities need to be averaged over the entirety of this spherical cell. It is computationally less efficient to set the innermost boundary of the domain to 0. Truncating the profile at the Rosseland mean optical depth of 30-50 is sufficient. (See [SuperLite methods paper](https://arxiv.org/abs/2305.17184) for more details.)

**Cell velocity coordinates:** Next two columns. The column 'vx_left' defines the velocity coordinate at the inner cell boundary, while 'vx_right' defines the velocity coordinate at the outer cell boundary.

*Note:*
  If only one column is provided for radial coordinates as described above, then only one column shall be provided for the velocity coordinates. In this case, the innermost velocity coordinate will be set to 0 corresponding to radial coordinate of 0.

The next set of columns contain the physical properties of the cells. The order in which these variables appear is not important. Following is the list of mandatory and optional variables. If the heads of the columns are misspelled, then those columns will be ignored. If it is a mandatory variable, then the code will stop with an error message. If it is optional column, then the code will run with its internal assumptions to evaluate the value of that variable, as described below.

**Cell mass:** The column must be labeled as "mass".<br>
**Cell volume (optional):** The column must be labeled as "vol". If missing, the volume will be calculated using the cell position coordinates.<br>
**Cell average temperature:** The column must be labeled as "temp". This is the average kinetic temperature/material temperature of the cell.<br>
**Cell radiation temperature (optional):** The column must be labeled as "rad_temp". This is the radiation temperature of the cell. If missing, it will be set equal to the average kinetic temperature/material temperature above.

The final set of columns contain the chemical abundances of the elements.

**Cell mass fractions:** These columns must be labeled using the chemical symbol of the element, such as 'H', 'He', 'Ni', etc. These labels are case insensitive. Do not include the atomic numbers in the labels.

*Notes:*
1. The code only uses the elements up to Z=30. All other elements will be ignored by the code. The isotopic abundances are not required by the code and will be ignored, if provided.
2. The code will normalize the abundances such that the sum of mass fractions up to Z=30 equals 1. The code will show a warning message if this sum diverges from 1 by a large fraction before normalizing it.
