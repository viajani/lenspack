# lenspack
[![Build Status](https://travis-ci.org/austinpeel/lenspack.svg?branch=master)](https://travis-ci.org/austinpeel/lenspack) [![Coverage Status](https://coveralls.io/repos/github/austinpeel/lenspack/badge.svg?branch=master)](https://coveralls.io/github/austinpeel/lenspack?branch=master)

---
> Author: <a href="http://www.cosmostat.org/people/austin-peel" target="_blank" style="text-decoration:none; color: #F08080">Austin Peel</a>  
> Email: <a href="mailto:austin.peel@cea.fr" style="text-decoration:none; color: #F08080">austin.peel@cea.fr</a>  
> Year: 2019  
---

This repository is a collection of python codes useful for the weak-lensing
analysis of galaxy catalogs and shear/convergence maps. The full documentation
can be found [here](https://austinpeel.github.io/lenspack/index.html "lenspack documentation").

## Contents

In progress.

## Dependencies

* numpy
* scipy
* astropy
* emcee (optional)
* nicaea (M. Kilbinger)

## Examples

### Peak detection

Suppose you have a galaxy catalog `cat` containing sky position columns `ra` and `dec`, along with ellipticity components `e1` and `e2`. You can bin the galaxies into pixels, invert the shear to obtain convergence (Kaiser & Squires, 1993), detect peaks above a given threshold, and plot the result as follows.

```python
import matplotlib.pyplot as plt
from lenspack.utils import bin2d
from lenspack.image.inversion import ks93
from lenspack.peaks import find_peaks2d

# Bin ellipticity components based on galaxy position into a 256 x 256 map
e1map, e2map = bin2d(cat['ra'], cat['dec'], v=(cat['e1'], cat['e2']), npix=256)

# Recover convergence via Kaiser-Squires inversion
kappaE, kappaB = ks93(e1map, e2map)

# Detect peaks on the convergence E-mode map
x, y, h = find_peaks2d(kappaE, threshold=0.05, include_border=True)

# Plot peak positions over the convergence
fig, ax = plt.subplots(1, 1, figsize=(7, 5.5))
mappable = ax.imshow(kappaE, origin='lower', cmap='bone')
ax.scatter(y, x, s=5, c='orange', alpha=0.7)  # reverse x and y due to array indexing
ax.set_axis_off()
fig.colorbar(mappable)
plt.show()
```

<p align="left">
<img src="https://github.com/austinpeel/lenspack/blob/master/examples/figures/peaks.png" alt="peaks" width="400"/>
</p>


### Wavelet transform

Take the starlet transform of an image using two different methods. Compare results to the equivalent aperture mass filter at a given scale.

```python
import numpy as np
import matplotlib.pyplot as plt
from lenspack.image.transforms import starlet2d, mr_transform
from lenspack.image.filters import aperture_mass

# Generate a test image
img = np.random.randn(256, 256)

# Take the starlet transform with 5 wavelet scales
st = starlet2d(img, nscales=5)
mrt = mr_transform(img, nscales=5)

# Compute the aperture mass map at scale 3 using the starlet filter
apm = aperture_mass(img, theta=2**4, filter='starlet')
```
