---
jupytext:
  cell_metadata_filter: -all
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.5
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# Tumor tissue organization with BAL stack

Here, we use BAL stack studied in a previous publication. 
See [Deciphering tumour tissue organization by 3D electron microscopy and machine learning](https://www.nature.com/articles/s42003-021-02919-z) for more details.

## Test

With MyST Markdown, you can define code cells with a directive like so:

```{code-cell}ipython3
:tags: ["hide-input"]
import SimpleITK as sitk

import numpy as np
import pandas as pd 
import scipy.ndimage as ndimage
import seaborn as sns
import scipy.stats as scipy
from sklearn.decomposition import PCA

import os

%matplotlib widget

import matplotlib as mpl
import matplotlib.pyplot as plt
from ..BioArchi.bioarchi import *
from ipywidgets import interact, fixed

print(sitk.Version())


mpl.rc('figure', max_open_warning = 0)

label_nuclei_path = "output/BAL_final2/label_nuclei_cleaned.nii.gz"
label_cells_with_complete_nuclei_path = "output/BAL_final2/label_cells_with_complete_nuclei.nii.gz"
label_cells_complete_cleaned_path = "output/BAL_final2/label_cells_complete_cleaned.nii.gz"

#background_path = "/Users/florianrobert/MEGAsync/Thèse/Donnees_SEM_2022/Task001_Nuclei/imagesTr/BAL_0000.nii.gz"

data_path = "output/BAL_final2/all_data_BAL.csv"
contour_path = "output/BAL_final2/contour_cells.nii.gz"
vessel_path = "output/BAL_final2/Vessel.nii.gz"

```

When your book is built, the contents of any `{code-cell}` blocks will be
executed with your default Jupyter kernel, and their outputs will be displayed
in-line with the rest of your content.

```{seealso}
Jupyter Book uses [Jupytext](https://jupytext.readthedocs.io/en/latest/) to convert text-based files to notebooks, and can support [many other text-based notebook files](https://jupyterbook.org/file-types/jupytext.html).
```

## Create a notebook with MyST Markdown

MyST Markdown notebooks are defined by two things:

1. YAML metadata that is needed to understand if / how it should convert text files to notebooks (including information about the kernel needed).
   See the YAML at the top of this page for example.
2. The presence of `{code-cell}` directives, which will be executed with your book.

That's all that is needed to get started!

## Quickly add YAML metadata for MyST Notebooks

If you have a markdown file and you'd like to quickly add YAML metadata to it, so that Jupyter Book will treat it as a MyST Markdown Notebook, run the following command:

```
jupyter-book myst init path/to/markdownfile.md
```
