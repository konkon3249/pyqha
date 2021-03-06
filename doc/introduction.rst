.. _introduction:


****************
Introduction
****************

:py:mod:`pyqha` is a Python package to perform quasi-harmonic and related calculations from total energies at 0 K, elastic constants at 0 K and phonon densities of states. The package provides Python functions to post-process the results of your favorite DFT code, such as Quantum Espresso [#QE]_ or VASP [#VASP]_, to obtain quasi-harmonic properties. It is meant to be imported in your own code or used to produce quasi-harmonic results (see the Tutorial part of this documentation). It is also meant for people who want to tinker with the code and adapt it to their own needs. Finally note that you may couple the package with some other available calculation Python tools, such as `ASE <https://wiki.fysik.dtu.dk/ase/about.html>`_ or `AiiDA <http://www.aiida.net/>`_.
The package is based on numpy, scipy and matplotlib libraries.

A non-exhaustive list of properties which can be obtained using :py:mod:`pyqha` is:

* quasi-harmonic Helmholtz energy for isotropic and anisotropic unit cells
* quasi-harmonic thermal expansions for isotropic and anisotropic unit cells
* quasi-harmonic bulk modulus for isotropic unit cells
* quasi-harmonic heat capacity for isotropic unit cells
* quasi-static elastic constants for anisotropic unit cells

Current features of the package include: 

* Fit the total energy :math:`E_{tot}(V)` with Murnaghan's equation of state
* Fit the total energy :math:`E_{tot}(a,b,c)`, where :math:`(a,b,c)` are the lattice parameters of hexagonal, tetragonal, orthorombic cells, using a quadratic or quartic polynomial
* Minimize the energy :math:`E_{tot}(V)+F_{vib}(V,T)` as a function of temperature with Murnaghan's equation of state
* Minimize the energy :math:`E_{tot}(a,b,c)+F_{vib}(a,b,c,T)` as a function of temperature using a quadratic or quartic polynomial
* Calculate the quasi-static elastic constant tensor as a function of temperature

The equations to obtain these properties are relatively simple, for an introduction on the quasi-harmonic approximation you can see Baroni et al., available online at `<https://arxiv.org/abs/1112.4977>`_ or [#Palumbo]_. For an introduction on quasi-static elastic constants see [#Wang]_ Have a look at the very good documentation of the *thermo_pw* fortran package available at `<http://qeforge.qe-forge.org/gf/project/thermo_pw/>`_.

.. [#QE] http://www.quantum-espresso.org/
.. [#VASP] https://www.vasp.at/
.. [#Palumbo] M. Palumbo, B. Burton, A. Costa e silva, B. Fultz, B. Grabowski, G. Grimvall, B. Hallstedt, O. Hellman, B. Lindahl, A. Schneider, P.E.A. Turchi, and W. Xiong. Physica Status Solidi (B) Basic Research, 251(1):14–32, 2014
.. [#Wang] Y. Wang, J. J. Wang, H. Zhang, V. R. Manga, S. L. Shang, L.-Q. Chen, and Z.-K. Liu. Journal of Physics Condensed Matter, 22:225404, 2010.


================
Installation
================

You can download all package files from GitHub (https://github.com/mauropalumbo75/pyqha) and then install it with the command:

.. code-block:: bash 

   sudo python setup.py install


The most useful functions for the common user are directly accessible from the :py:mod:`pyqha`. You can import all of them as:

.. code-block:: python 

   from pyqha import *

or you can import only the ones you need. The above command also makes available a number of useful constants that you can use for unit conversions.

More functions are available as submodules. See the related documentation for more details. Note, however, that most of these functions are less well documented and are meant for advanced users or if you want to tinker with the code.

================
General notes
================

----------------------------
Bravais lattice (*ibrav*)
----------------------------

The parameter *ibrav*, which occurs in many functions of this package, identifies the Bravais lattice of the system as in Quantum Espresso. Currently, only cubic (*ibrav=1,2,3*), hexagonal (*ibrav=4*), tetragonal (*ibrav=6,7*), orthorhombic (*ibrav=8,9,10,11*) lattices are implemented. Note that only the variation of phonon frequencies over a grid :math:`(a,b,c)` of lattice parameters can be carried out for now. Angles and internal degrees of freedom (atomic positions) cannot be considered. Thus, different *ibrav* can give the same grid:

    +-----------------------------+----------------------------------+----------------------------------+
    | *ibrav*                     | Grid                             | Fitting polynomial               |
    +-----------------------------+----------------------------------+----------------------------------+ 
    | 1,2,3                       | :math:`(a,a,a)`                  | 1 variable, 2nd or 4th degree    |
    +-----------------------------+----------------------------------+----------------------------------+ 
    | 4,6,7                       | :math:`(a,a,c)`                  | 2 variables, 2nd or 4th degree   |
    +-----------------------------+----------------------------------+----------------------------------+ 
    | 8,9,10,11                   | :math:`(a,b,c)`                  | 3 variables, 2nd or 4th degree   |
    +-----------------------------+----------------------------------+----------------------------------+ 


----------------------------
Choice of the grid points
----------------------------

:py:mod:`pyqha` essentially relies on the calculation of total energies, phonon frequencies and elastic constants using one of the above grids according to the symmetry of the unit cell. For the choice of the values of grid points you should follow the following guidelines:

 - the minimum of the energy must be close to the center of the grid to guarantee better results. This of course can only be evaluated *a posteriori* or based on experimental values. Since the minimum changes with temperature because of thermal expansion, the size of the grid must be large enough for it not to get too close to the boundaries at high temperature.
 - the step between the grid points must be small enough to guarantee a correct fitting of the energy but at the same time you must limit the total number of points. The same is true for the total width of the grid. This is especially important for phonon frequencies which are more computational demanding than the total energies at 0 K.
 - typically, 9 volume values are sufficient for fitting the Murnaghan for most purposes. Typical values for a 2D grid are 25 values (5x5) and 125 values for a 3D grid (5x5x5).
 
 .. Warning::
   Testing the choice of the grid points is essential to obtain reliable results!  

----------------------------
Plotting
----------------------------

:py:mod:`pyqha` uses the *matplotlib* library for Plotting. Some functions in the package are simply useful wrappers of *matplotlib* functions for common uses. They return a *matplotlib* object which can be further adapted to specific needs and personal taste.