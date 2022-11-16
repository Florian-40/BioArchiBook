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


```{code-cell}ipython3
:tags: ["hide-cell"]
import SimpleITK as sitk

import numpy as np
import pandas as pd 
import scipy.ndimage as ndimage
import seaborn as sns
import scipy.stats as scipy
from sklearn.decomposition import PCA

import os
os.chdir('/Users/florianrobert/Seafile/These/Python/BioArchi')

%matplotlib widget

import matplotlib as mpl
import matplotlib.pyplot as plt
from bioarchi import *

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
contour=2

```

When your book is built, the contents of any `{code-cell}` blocks will be
executed with your default Jupyter kernel, and their outputs will be displayed
in-line with the rest of your content.

```{seealso}
Jupyter Book uses [Jupytext](https://jupytext.readthedocs.io/en/latest/) to convert text-based files to notebooks, and can support [many other text-based notebook files](https://jupyterbook.org/file-types/jupytext.html).
```

## Volume

In this section we analyze volume of complete cells and their nuclei. With these graphs we can conclude an inverse correlation between volume of complete cells and their distance to vessel.

```{code-cell}ipython3
:tags: ["hide-input", "thebe-init"]

# read data frame 
df = pd.read_csv(data_path, index_col=0)
    
    
############################################## data for slice by slice display ########################
label_vessel2 = sitk.ReadImage(vessel_path)
contour_cells = sitk.ReadImage(contour_path)
    
label_nuclei_cleaned = sitk.ReadImage(label_nuclei_path)
label_cells_complete_cleaned = sitk.ReadImage(label_cells_complete_cleaned_path)
label_cells_with_complete_nuclei = sitk.ReadImage(label_cells_with_complete_nuclei_path)
    
    
    
############################################# VOLUME FOR COMPLETE CELLS AND THEIR NUCLEI ###############################
########################################################################################################################
    
# Correlation graph for cells 
stat = df.loc[:,['Volume of cell', 'Distance of cell to vessel']]
stat =stat.loc[df.loc[:,'Volume of cell'].notnull(),:]
plt.figure()
slope, intercept, r_value, pv, se = stats.linregress(stat.iloc[:,1].tolist(), stat.iloc[:,0].tolist())
sns.regplot(data=stat, x= stat.columns[1], y=stat.columns[0], ci=None, line_kws={'label':'$y=%3.7s*x+%3.7s$'%(slope, intercept)})
plt.xlabel('Distance to vessel ($\mu m$)')
plt.ylabel('Volume ($\mu m^3$)')
plt.xlim(0,50)
plt.ylim(0,2000)
plt.title ('Correlation between volume of complete cells \n and distance from their center of gravity to vessel.')
plt.legend()
plt.show()

print(stats.spearmanr(stat.iloc[:,0].values, stat.iloc[:,1].values))
    
    
    
# Correlation graph for nuclei 
stat = df.loc[:,['Volume of nuclei', 'Distance of nuclei to vessel']]
stat =stat.loc[df.loc[:,'Volume of cell'].notnull(),:]

plt.figure()
slope, intercept, r_value, pv, se = stats.linregress(stat.iloc[:,1].tolist(), stat.iloc[:,0].tolist())
sns.regplot(data=stat, x= stat.columns[1], y=stat.columns[0], ci=None, line_kws={'label':'$y=%3.7s*x+%3.7s$'%(slope, intercept)})
plt.xlabel('Distance to vessel ($\mu m$)')
plt.ylabel('Volume ($\mu m^3$)')
plt.xlim(0,50)
plt.ylim(0,500)
plt.title ('Correlation between volume of nuclei \n and disstance from their center of gravity to vessel.')
plt.legend()
plt.show()

print(stats.spearmanr(stat.iloc[:,0].values, stat.iloc[:,1].values))
    
    
# Slice by slice display of cells colored by volume of cells. 
data = df.loc[:,'Volume of cell']
data =data.loc[df.loc[:,'Volume of cell'].notnull()]
    

#plt.figure()
volume_cells_complete_cleaned = simple_display_slice_by_slice(
            contour_cells, 
            label_vessel2,
            data, 
            data_3D = False, 
            label_cells_complete_cleaned = label_cells_complete_cleaned ,
            title = 'Cell sizes related to distance from blood capillary', 
            legend_title='Volume (in $\mu m ^3$)', 
           figsize=(8,4))
plt.show()
    
   

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
