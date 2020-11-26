[![Abcdspec-compliant](https://img.shields.io/badge/ABCD_Spec-v1.1-green.svg)](https://github.com/brain-life/abcd-spec)
[![Run on Brainlife.io](https://img.shields.io/badge/Brainlife-bl.app.140-blue.svg)](https://doi.org/10.25663/brainlife.app.140)

# app-ROIsfromAtlas
Extract single, multiple, or combinations of ROIs from a parcellation/atlas by using the index (number) of the entires in the atlas.  See User Input for specific instructions on how to do this.

**NOTE** all output is in the same space as the input atlas.  Thus, **users should take care to ensure that their input atlas is in the appropriate coordinate space for their intended application**.  Alternatively, one can ensure that downstream data objects (i.e. [DWI](https://brainlife.io/datatype/58c33c5fe13a50849b25879b) or [tractograms](https://brainlife.io/datatype/5907d922436ee50ffde9c549) are in the same coordinate space as this atlas (and, by extension the structure it was derived from as well).

### Authors
- Daniel Bullock (dnbulloc@iu.edu)

### Contributors
- Daniel Bullock (dnbulloc@iu.edu)
- Soichi Hayashi (hayashis@iu.edu)

### Project Director
- Franco Pestilli (franpest@indiana.edu)

### Funding
[![NSF-BCS-1734853](https://img.shields.io/badge/NSF_BCS-1734853-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1734853)
[![NSF-BCS-1636893](https://img.shields.io/badge/NSF_BCS-1636893-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1636893)
[![NSF-ACI-1916518](https://img.shields.io/badge/NSF_ACI-1916518-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1916518)
[![NSF-IIS-1912270](https://img.shields.io/badge/NSF_IIS-1912270-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1912270)
[![NIH-NIBIB-R01EB029272](https://img.shields.io/badge/NIH_NIBIB-R01EB029272-green.svg)](https://grantome.com/grant/NIH/R01-EB029272-01)
[![NIMH-T32-5T32MH103213-05](https://img.shields.io/badge/NIMH_T32-5T32MH103213--05-blue.svg)](https://projectreporter.nih.gov/project_info_description.cfm?aid=9725739)

### References 
[Avesani, P., McPherson, B., Hayashi, S. et al. The open diffusion data derivatives, brain data upcycling via integrated publishing of derivatives and reproducible open cloud services. Sci Data 6, 69 (2019).](https://doi.org/10.1038/s41597-019-0073-y)

## Running the App 

### On Brainlife.io

Visit https://doi.org/10.25663/brainlife.app.140 to run this app on the brainlife.io platform.  Requires a [parcellation](https://brainlife.io/datatype/5c1a7489f9109beac4a88a1f) type input.

### Running Locally (on your machine) using singularity & docker

Because this is compiled code which runs on singularity, you can download the repo and run it locally with minimal setup.  Ensure that you have singularity and use the main (not main.m) file with an appropriate (for your local file structure) config.json file.

### Running Locally (on your machine)

Pull the [wma toolkit repo](https://github.com/DanNBullock/wma_tools)
Ensure that [vistasoft](https://github.com/vistalab/vistasoft)and [spm](https://www.fil.ion.ucl.ac.uk/spm/software/) are installed.

Rather than running the main or main.m, simply use [the workhorse function for this app](https://github.com/brainlife/app-ROIsfromAtlas/blob/b2f90c9edade329d9f69020461e18f01cd6fc7bd/main.m#L81), which can be found [here](https://github.com/DanNBullock/wma_tools/blob/master/ROI_Tools/amalgumROIsFromInput.m) in the [wma_tools repo](https://github.com/DanNBullock/wma_tools): 

The documentation within that app should be sufficent to make use of the functionality.

### Sample Datasets

Visit brainlife.io and explore [projects](https://brainlife.io/projects) to find projects which contain [parcellations](https://brainlife.io/datatype/5c1a7489f9109beac4a88a1f)

## Input

This app requires that a [parcellation](https://brainlife.io/datatype/5c1a7489f9109beac4a88a1f) type input be passed to it.

### User Input

### roiPairs

This input is where the user specifies the ROIs that they would like extracted from the input atlas/parcellation.  

Each row of this field constitutes a request for an ROI output.  As such, if your request spans 5 lines, you will receive 5 output ROIs.

What you type on each row determines what each of these output ROIS contains.  The specific user input is in the form of a series of integers.  For example "1 2 3 4 5" (without quotes).  An input of this type (a single line with "1 2 3 4 5" (without quotes)) would return **a single output ROI** which represents the merging of whatever voxel labels are associated with 1 2 3 4 and 5 in the input parcellation.

**NOTE** to determine which labels are associated with which structure in a parcellation/atlas, refer back to the key.txt or label.json files in the [parcellation data object](https://brainlife.io/datatype/5c1a7489f9109beac4a88a1f)

To request multiple output ROIs split your request across multiple lines.  For example an input request that looks like this:

1  
2  
3  
4  
5  

Would return 5 separate [ROIs (in the output ROI directory)](https://brainlife.io/datatype/5be9ea0315a8683a39a1ebd9), each of which features a single label from the input parcellation/atlas.

Alternatively an input request that looks like this:

21 5 16 32 8  
21 77 1 2  
3  
55 18 9  
132 64 199 100 7 11  

Would return 5 separate ROIs, each of which constitutes a merging of all labels on the respective lines.

### smoothKernel

The smoothKernel parameter input allows you to specify whether (and to what degree) you would like your ROIs to be inflated after they are extracted from the source parcellation/atlas.  This inflation is acheived via the application of a gaussian smoothing kernel.

An empty setting, or a setting of 0 or 1 entails no inflation is performed.

A setting with **an odd integer value** will apply a gaussian inflation with the specified **diameter**.  **EVEN VALUES WILL NOT WORK**

## Output

This app outputs a [standard ROI data output](https://brainlife.io/datatype/5be9ea0315a8683a39a1ebd9) in which the output ROI names are simply the (individual) input requests (i.e. lines) with a "ROI.nii.gz" appended to the end.

#### Product.json

Not relevant for this App as it does not genrate processed data. 

### Dependencies

This App only requires [singularity](https://www.sylabs.io/singularity/). If you don't have singularity, you will need to install following dependencies.  

https://singularity.lbl.gov/docs-installation

Alternatively, local use may require several local repositories including
[wma_tools](https://github.com/DanNBullock/WiMSE)
[vistasoft](https://github.com/vistalab/vistasoft)
[spm](https://github.com/spm/spm12)
