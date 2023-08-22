---
description: Basic overview of the Big Climate Data Pipeline (BCDP)
---

# Intro to BCDP



BCDP is a python library for running basic data analysis workflows of multiple climate datasets, and includes functionality to subset, resample, and regrid datasets with different spatial and temporal resolutions onto a common grid. BCDP heavily leverages the pydata stack which includes xarray, pandas, and dask. Support for reading data locally or directly from cloud storage buckets (S3 or GCS) is also included.



### Installation

The preferred method to install BCDP is through conda:

```
conda install bcdp
```



### Example Usage

The below example regrids air temperature from a CMIP6 climate model archived on S3 onto a 5 degree global lat/lon grid.

```
import os
import glob
import numpy as np
import bcdp
import pandas as pd

# Dataset Options
dataset = 's3://cmip6-pds/CMIP6/CMIP/NCAR/CESM2-WACCM/historical/r1i1p1f1/Amon/tas/gn/v20190227/'

# Regridding Options
backend = 'scipy'
method = 'linear'
dlon = 5
dlat = 5

# Output
nc_file = 'CMIP_example.nc' # type: stage-out

# Load data
ens = bcdp.load_bucket(dataset, anon=True)

# Perform regridding to 5 degree lat/lon grid
output_grid = bcdp.utils.grid_from_res((dlon, dlat), ens.overlap)
ens_u = ens.regrid(backend=backend, method=method, output_grid=output_grid, clean=False)

# Convert output to xarray DataArray object and save the result to netCDF
da = ens.bundle('Temperature').first
da.to_netcdf(nc_file)
```
