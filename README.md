<img src="http://raw.github.com/wiki/ahkab/ahkab/images/logo_small.png" alt="Monkeying around" style="width: 80px;"/> ahkab
===========================================================================================================================

**a SPICE-like electronic circuit simulator written in Python**

The code should be easy to read and modify, the main language is Python -- 2 or 3 -- and it is platform-independent.[![githalytics.com alpha](https://cruel-carlota.gopagoda.com/3f4b146d6a15f66802f1906e5cf4f68c)](http://githalytics.com/ahkab/ahkab)

News!
-----

-   Ahkab v0.13 was released on Feb 17 2015, including several bugfixes, new features and additional documentation. It is recommended to upgrade. Check out [the release notes](https://github.com/ahkab/ahkab/releases/tag/v0.13) for more!
-   The whole codebase has been going through a (yet incomplete) refactoring and documenting effort. The [new documentation is available on RTD](http://ahkab.readthedocs.org/en/latest/).

My resources are limited these days, so the much-needed work is proceeding slowly, albeit hopefully steadily. If you are interested and you would like to contribute to refactoring or documenting a particular feature, it would be very welcome.

[![Build Status](https://travis-ci.org/ahkab/ahkab.png?branch=master)](https://travis-ci.org/ahkab/ahkab) [![Coverage Status](https://coveralls.io/repos/ahkab/ahkab/badge.png?branch=master)](https://coveralls.io/r/ahkab/ahkab?branch=master) [![PyPi version](http://img.shields.io/badge/version-0.13-brightgreen.png)](https://pypi.python.org/pypi/ahkab/) [![GPLv2 license](http://img.shields.io/badge/license-GPL%20v2-brightgreen.png)](https://raw.githubusercontent.com/ahkab/ahkab/master/LICENSE) [![DOI](https://zenodo.org/badge/doi/10.5281/zenodo.13774.svg)](http://dx.doi.org/10.5281/zenodo.13774)

Supported simulations:
----------------------

-   Numeric:
    -   **Operating point**, with guess computation to speed up the solution. See example: [Downscaling current mirror](https://github.com/ahkab/ahkab/wiki/Example:-OP-simulation)
    -   **DC sweep**
    -   **Transient analysis**, available differentiation formulas: implicit Euler, trapezoidal, gear orders from 2 to 5. See for example the [simulation of a Colpitts Oscillator](https://github.com/ahkab/ahkab/wiki/Example:-Transient-simulation-1).
    -   **AC analysis**
    -   **PZ** analysis
    -   **Periodic steady state analysis** of non-autonomous circuits, *time* *domain* shooting and brute-force algorithms.
-   Symbolic:
    -   **Small signal analysis**, AC or DC, with extraction of transfer functions, DC gain, poles and zeros. Various [symbolic analysis examples on this page](https://github.com/ahkab/ahkab/wiki/Example:-Symbolic-simulation).

The results are saved to disk, plotted or printed to stdout and can be read/processed by the most common tools (eg. [Octave](http://www.gnu.org/software/octave/), [gnuplot](http://www.gnuplot.info/), [Matlab](http://www.mathworks.com/products/matlab/), [gwave](http://www.telltronics.org/software/gwave/) and others)

Install
-------

The program requires:

-   the Python interpreter version 2 or 3 (at least v.2.6 for Python2, v.3.3 for Python3),
-   numpy>=1.7.0, scipy>=0.14.0, sympy>=0.7.5 and tabulate>=0.7.3.

Matplotlib is strongly recommended and no plotting will work without.

If you need more information about the dependencies, check the [Install notes](https://github.com/ahkab/ahkab/wiki/Install:-Notes).

Usage
-----

### 1. `ahkab` can be run as a Python library

<img src="https://rawgithub.com/ahkab/ahkab/master/doc/images/readme_example/pbf.svg" alt="Example schematic: a 5th order 1kHz band-pass Butterworth filter"/>

``` {.sourceCode .python}
from ahkab import new_ac, run
from ahkab.circuit import Circuit
from ahkab.plotting import plot_results # calls matplotlib for you
import numpy as np

# Define the circuit
cir = Circuit('Butterworth 1kHz band-pass filter')
cir.add_vsource('V1', 'n1', cir.gnd, dc_value=0., ac_value=1.)
cir.add_resistor('R1', 'n1', 'n2', 50.)
cir.add_inductor('L1', 'n2', 'n3', 0.245894)
cir.add_capacitor('C1', 'n3', 'n4', 1.03013e-07)
cir.add_inductor('L2', 'n4', cir.gnd, 9.83652e-05)
cir.add_capacitor('C2', 'n4', cir.gnd, 0.000257513)
cir.add_inductor('L3', 'n4', 'n5', 0.795775)
cir.add_capacitor('C3', 'n5', 'n6', 3.1831e-08)
cir.add_inductor('L4', 'n6', cir.gnd, 9.83652e-05)
cir.add_capacitor('C4', 'n6', cir.gnd, 0.000257513)
cir.add_capacitor('C5', 'n7', 'n8', 1.03013e-07)
cir.add_inductor('L5', 'n6', 'n7', 0.245894)
cir.add_resistor('R2', 'n8', cir.gnd, 50.)

# Define the analysis
ac1 = new_ac(2.*np.pi*.97e3, 2.*np.pi*1.03e3, 1e2, x0=None)

# run it
res = run(cir, ac1)

# plot the results
plot_results('5th order 1kHz Butterworth filter', [('|Vn8|',"")], res['ac'],
             outfilename='bpf_transfer_fn.png')
```

<img src="https://rawgithub.com/ahkab/ahkab/master/doc/images/readme_example/bpf_results.svg" alt="Example: AC simulation results"/>

#### 2. `ahkab` can be run from the command line with a netlist file

The syntax is:

    `$ python ahkab -o graph.dat <netlist file>`

See `ahkab --help` for command line switches, [also online on the documentation pages.](http://ahkab.readthedocs.org/en/latest/help/Command-Line-Help.html)

### Documentation

The [documentation is available on RTD](http://ahkab.readthedocs.org/en/latest/).

There, you can find a [documentation](http://ahkab.readthedocs.org/en/latest/ahkab.html) and [examples](http://ahkab.readthedocs.org/en/latest/examples/Python_API.html) regarding how to simulate from a Python script.

Refer to the [netlist syntax page](http://ahkab.readthedocs.org/en/latest/help/Netlist-Syntax.html) if you prefer to write netlist files that describe the circuit.

Experience with running SPICE or related commercial simulators can be very useful: this is not for the faint of heart.

### Development model

-   The development happens on the [github repository](https://github.com/ahkab/ahkab),
-   Mostly on the master branch, with feature branch being created only for special purposes or non-trivial features.
-   Snapshots are released on a (hopefully) regular basis and are available on the [Releases pages, complete with changelog](https://github.com/ahkab/ahkab/releases) and on [PYPI](https://pypi.python.org/pypi/ahkab/)

Patches and pull requests are welcome!

### How this project was born

This project was born when I was an enthusistic undergrad, apparently with plenty of free time, attending "Simulazione Circuitale" (*Circuit Simulation*) taught by [Prof. A. Brambilla](http://brambilla.dei.polimi.it/) back in Italy at the Polytechnic University of Milan.

I am grateful to prof. Brambilla for teaching one of the most interesting courses of my university years. -GV

### Bugs and patches

Does it work? Bugs? Do you have patches? Did you run some noteworthy simulation? Let me know! Feedback is very welcome, my [email address](http://tinymailto.com/5310) is available after a captcha.

### Support the development with a donation

If you wish to support the development of `ahkab`, ***please donate to cancer research:***

-   [Association for International Cancer Research (eng)](http://www.aicr.org.uk/donate.aspx),

or

-   [Fond. IRCCS Istituto Nazionale dei Tumori (it)](http://www.istitutotumori.mi.it/modules.php?name=Content&pa=showpage&pid=24).

### Credits

**Authors:** [Giuseppe Venturini](https://github.com/ggventurini), with contributions from [Ian Daniher](https://github.com/itdaniher) and [Rob Crowther](https://github.com/weilawei).

**Code:** the module `py3compat.py` is (c) 2013 - the Jinja team.

**Dependencies:** many thanks to the authors of `numpy`, `scipy`, `sympy`, ``matplotlib`` and ``tabulate``!

