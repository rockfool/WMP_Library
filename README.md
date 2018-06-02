# Windowed Multipole Library

This repository contains neutron cross section data for different
isotopes in [windowed multipole (WMP)] format which allows for analytic
on-the-fly Doppler broadening.
The WMP representation of cross section has been used by [OpenMC].

As detailed in [1], the poles of the nuclides in this library have been generated
by either conversion of resolved resonance parameters or using Vector Fitting.
Then the [OpenW] code is used to process the multipole cross sections into
windowed multipole library.

The current library includes 423 nuclides processed from [ENDF/B VII.1] library,
with a target maximum relative error for all cross sections of 0.1%.
An overview of the library can be found in the table in
'[Nuclides.md](./Nuclides.md)'.

More details about the windowed multipole method or multipole representation can
be found in [2-4].

- [1] J. Liang, X. Peng, S. Liu, C. Josey, B. Forget and K. Smith. "Processing
  Of A Comprehensive Windowed Multipole Library via Vector Fitting." PHYSOR
  2018: Reactor Physics paving the way towards more efficient systems. Cancun,
  Mexico, April 22-26, 2018.
- [2] C. Josey, P. Ducru, B. Forget, and K. Smith. "Windowed multipole for cross
  section Doppler broadening." Journal of Computational Physics, volume 307,
  pp. 715–727 (2016).
- [3] B. Forget, S. Xu, and K. Smith. "Direct Doppler broadening in Monte Carlo
  simulations using the multipole representation." Annals of Nuclear Energy,
  volume 64, pp. 78–85 (2014).
- [4] R. N. Hwang. "A rigorous pole representation of multilevel cross sections
  and its practical applications." Nuclear Science and Engineering, volume
  96(3), pp. 192–209 (1987).

## Library format specifications

Windowed multipole data is stored in HDF5 files, containing the energy
boundaries, window structure, poles/residues, and curve fit coefficients, etc.

Detailed specifications can be found in [Windowed Multipole Library Format].

## Download

[Git LFS] is used to store the binary HDF5 files.
To download the real WMP library, you need to firstly install [Git LFS] and then
clone this repository.

``` bash
$ git clone https://github.com/mit-crpg/WMP_Library.git 
```

## Usage

With the windowed multipole library, it is convenient and efficient to compute
cross sections at arbitrary temperature (in 0 K-3000 K range) and energy
(in the resolved resonance range) for 4 reactions: total, elastic scattering,
radiative capture, and fission.

An excellent reference is [OpenMC], which implements WMP in both the transport
solver and the [OpenMC Python API].
For example, the following script demonstrates how to utilize WMP library using
the nuclear data interface [WindowedMultipole].

``` python
# load a library
import openmc.data
u238_multipole = openmc.data.WindowedMultipole.from_hdf5('092238.h5')

# evaluate cross sections at a given energy and temperature
total_xs, absorption_xs, fission_xs = u238_multipole(E=1.0, T=300.)

# comparison with ACE library (HDF5 format used in OpenMC)
u238_ace = openmc.data.IncidentNeutron.from_hdf5('U238.h5')
energy = np.logspace(np.log10(u238_multipole.start_E), np.log10(u238_multipole.end_E), 1E4)
total_xs_wmp = u238_multipole(energy, T=293.75)[0]
total_xs_ace = u238_ace[1].xs['294K'](energy)
# then you can plot the cross sections with energies
```

## Reporting

 - Submit GitHub issues: https://github.com/mit-crpg/WMP_Library/issues
 - Contact MIT [Computational Reactor Physics Group (CRPG)]

[windowed multipole (WMP)]: http://openmc.readthedocs.io/en/latest/methods/cross_sections.html#windowed-multipole-representation
[OpenMC]: https://github.com/mit-crpg/openmc
[OpenW]: https://github.com/mit-crpg/WHOPPER
[ENDF/B VII.1]: http://www.nndc.bnl.gov/endf/b7.1/
[Windowed Multipole Library Format]: http://openmc.readthedocs.io/en/latest/io_formats/data_wmp.html#io-data-wmp
[Git LFS]: https://git-lfs.github.com
[OpenMC Python API]: http://openmc.readthedocs.io/en/latest/pythonapi/index.html
[WindowedMultipole]: https://github.com/mit-crpg/openmc/blob/develop/openmc/data/multipole.py
[Computational Reactor Physics Group (CRPG)]: http://crpg.mit.edu/