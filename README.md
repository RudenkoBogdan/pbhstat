pbhstat

A Python package for calculating the primordial black hole (PBH) mass function from a given primordial power spectrum with multiple statistical formalisms.

📦 Installation

We recommend installing pbhstat inside a Python virtual environment or conda environment.

pip install pbhstat

Alternatively, you can clone the repository directly from GitHub for development purposes:

git clone https://github.com/pipcole/pbhstat.git
If using the code this way, ensure that the following Python dependencies are installed manually:

numpy
scipy
matplotlib
tqdm
🖥️ Compatibility

The code has been tested with:

Python 3.9.15 on macOS Sonoma 14.1 (Apple M3 Pro)
Python 3.12.3 on Ubuntu 24.04 LTS (Lenovo Yoga 7)
Python 3.11 in Google Colab
📓 Quick Start

Two example Jupyter notebooks are provided:

Custom power spectrum input
Pre-defined piecewise power spectrum
1. Import Modules
import numpy as np
import pickle

import pbhstat

from pbhstat.power_spectrum import PowerSpectrum
from pbhstat.mass_variance import MassVariance
from pbhstat.collapse_stats import PressSchechterModel, PeaksTheoryModel, BroadPeakModel
from pbhstat.mass_function import MassFunction
from pbhstat.plot_utils import plot_power_spectrum, plot_mass_variance, plot_mass_function
2. Define Power Spectrum
Custom log-normal example:

k_values = np.logspace(3, 7, 3000)
Ak = 0.008
Deltak = 1
kpeak = 1e5

P_k_custom = Ak * np.exp(-0.5 * (1/(np.sqrt(2 * np.pi) * Deltak)) * (np.log(k_values / kpeak) / Deltak)**2)

ps_custom = PowerSpectrum(
    shape='custom',
    k_values=k_values,
    P_k_values=P_k_custom
)
Or load from file:

with open("path_to_k_array.pickle", "rb") as f:
    k_values = pickle.load(f)

with open("path_to_P_k_array.pickle", "rb") as f:
    P_k_custom = pickle.load(f)

ps_custom = PowerSpectrum(
    shape='custom',
    k_values=k_values,
    P_k_values=P_k_custom
)
Alternatively, use a built-in piecewise spectrum:

k_values = np.logspace(3, 7, 3000)

ps_piecewise = PowerSpectrum(
    shape='piecewise',
    amplitude=0.008,
    k_star=1e5,
    ng=4,
    nd=2,
    k_values=k_values
)
3. Instantiate Mass Variance
mv_piecewise = MassVariance(
    window='realtophat',
    power_spectrum=ps_piecewise,
    statistics='nonlinear',
    cutoff=False
)
4. Evaluate Mass Function
mass_function = MassFunction(
    mass_variance=mv_piecewise,
    statistics='nonlinear',
    K=4,
    vcorr=True,
    gamma=0.36
)

mpbh, f_mpbh = mass_function.evaluate(k_values, mpbh_vals=50)
fpbh = mass_function.fpbh(f_mpbh, mpbh)

5. Plot Results
plot_power_spectrum(k_values, ps_piecewise(k_values))

R_values = 1 / k_values
sigma0sq_piecewise = mv_piecewise.evaluate(k_values)
plot_mass_variance(R_values, sigma0sq_piecewise)

plot_mass_function(mpbh, f_mpbh, fpbh_val=fpbh)
📈 Plotting Constraints with PBHbounds

To overlay your calculated mass function with observational constraints, use the PBHbounds repository.

Save your mass function to the PBHbounds directory:

from pbhstat.plot_utils import bounds_utility
bounds_utility(mpbh, f_mpbh, 'path_to_PBHbounds_directory')
Then run the modified script PlotPBHbounds.py from within that directory (available here).

Note: Overlaying extended mass functions on monochromatic constraints is only an approximation. This utility is best suited to narrow mass functions. For consistent constraint conversion, see: Bellomo et al., 2017.
📚 Reference

If you use this package, please cite the accompanying paper (reference to be added).