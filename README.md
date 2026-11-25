<h1 align="center">Generate ROIs from an atlas</h1>

<p align="center">
  Single, multiple or merged regions of interest, as NIfTI files, from a volumetric parcellation selected by label index.
</p>

<p align="center">
  <a href="https://github.com/brainlife/abcd-spec"><img src="https://img.shields.io/badge/ABCD_Spec-v1.1-green.svg" alt="ABCD Spec v1.1"></a>
  <a href="https://doi.org/10.25663/brainlife.app.140"><img src="https://img.shields.io/badge/Brainlife-brainlife.app.140-blue.svg" alt="Run on brainlife.io"></a>
  <a href="https://github.com/brainlife/app-ROIsfromAtlas/blob/master/LICENSE"><img src="https://img.shields.io/badge/License-MIT-lightgrey.svg" alt="MIT license"></a>
</p>

> **This branch, `1.0`, is the brainlife.io App Generate ROIs from an atlas** ([doi:10.25663/brainlife.app.140](https://doi.org/10.25663/brainlife.app.140)).

## At a glance

<!-- brainlife-app:begin (generated from the App manifest; edit the manifest, not this block) -->

> **Extracts single or merged ROIs as NIfTI files from a volumetric parcellation by label index, with optional smoothing/inflation, using wma_tools.**

For each line (or semicolon-separated group) in roiPairs, merges the listed parcellation indices into one ROI with bsc_roiFromAtlasNums and writes rois/ROI<n>.nii.gz. Output ROIs are in the same space as the input parcellation, so make sure it matches the data you will use them with.

| | |
|---|---|
| **Inputs** | `neuro/parcellation/volume` |
| **Outputs** | `neuro/rois` |
| **Container** | `brainlife/mcr:r2019a` |
| **Resources** | 8 CPUs · 18 h walltime |
| **Run it** | [doi.org/10.25663/brainlife.app.140](https://doi.org/10.25663/brainlife.app.140) |

#### Inputs

| Input | Datatype | Required | Description |
|---|---|---|---|
| `atlas` | `neuro/parcellation/volume` | yes | Volumetric parcellation (NIfTI) with its label key. Must be in the space where the ROIs will be used. |

#### Outputs

| Output | Datatype | Description |
|---|---|---|
| `rois` | `neuro/rois` | ROIs (ROI1.nii.gz, ROI2.nii.gz, ...), one per entry of roiPairs, in the space of the parcellation. |

<details>
<summary><b>Parameters</b> (2)</summary>

| Parameter | Type | Default | Description |
|---|---|---|---|
| `roiPairs` | string |  | This is where you enter in the indexes for the parcellation regions you would like to convert into ROIs (disregard the "pairs" implication). Must be in appropriate format, for example: 23 95 2 8 1 2 12 34 8 3 5 4 5 236 86 72 12 55 line breaks separate ROIs |
| `smoothKernel` | number | `1` | Smoothing Kernel for ROI. Odd, whole number values only. Values larger than 1 result in ROI inflation. 0 performs no inflation. https://www.mathworks.com/help/matlab/ref/smooth3.html |

</details>

<!-- brainlife-app:end -->

> [!NOTE]
> All output is in the same space as the input atlas. Make sure the input atlas is in the appropriate coordinate space for your intended application, or that downstream data (for example [DWI](https://brainlife.io/datatype/58c33c5fe13a50849b25879b) or [tractograms](https://brainlife.io/datatype/5907d922436ee50ffde9c549)) are in the same space as the atlas and the structure it was derived from.

## Running the App

### On brainlife.io

Open the App at [doi.org/10.25663/brainlife.app.140](https://doi.org/10.25663/brainlife.app.140), choose a [parcellation](https://brainlife.io/datatype/5c1a7489f9109beac4a88a1f) (`neuro/parcellation/volume`) from your project, enter the label indices in `roiPairs`, and press **Execute**. The App writes a [`neuro/rois`](https://brainlife.io/datatype/5be9ea0315a8683a39a1ebd9) dataset to your project. To find which label belongs to which structure, look at the `key.txt` or `label.json` file of the parcellation.

#### `roiPairs`

Each line of this field (disregard the "pairs" in the name) is a request for one output ROI, so a request that spans 5 lines returns 5 ROIs. Lines can also be separated with `;`.

Each line is a series of label indices separated by spaces. A single line `1 2 3 4 5` returns **one** ROI that merges the voxels of labels 1, 2, 3, 4 and 5. To get one ROI per label, put each index on its own line:

```text
1
2
3
4
5
```

A request such as

```text
21 5 16 32 8
21 77 1 2
3
55 18 9
132 64 199 100 7 11
```

returns 5 ROIs, each merging all the labels on its line.

#### `smoothKernel`

Whether, and how much, to inflate the ROIs after they are extracted, using a Gaussian smoothing kernel ([`smooth3`](https://www.mathworks.com/help/matlab/ref/smooth3.html)). A value of 0 or 1 means no inflation (default 1). An **odd integer** applies a Gaussian inflation with that diameter; **even values do not work**.

### Locally

You need [Singularity/Apptainer](https://apptainer.org/). The App is compiled MATLAB code and runs in the `brainlife/mcr:r2019a` container, so it can run locally with minimal setup.

1. Clone this repository and check out the registered version (`git checkout 1.0`).
2. Create `config.json` with the path to your parcellation and your requests (see `config.json_sample`):

   ```json
   {
       "atlas": "/input/parcellation/parc.nii.gz",
       "roiPairs": "23 95 2 8\n1 2 12 34\n8 3 5\n4 5",
       "smoothKernel": 3
   }
   ```

3. Run the App with `main` (not `main.m`):

   ```bash
   ./main
   ```

Without Singularity, you can use the workhorse function of this App directly in MATLAB: [`amalgumROIsFromInput.m`](https://github.com/DanNBullock/wma_tools/blob/master/ROI_Tools/amalgumROIsFromInput.m) in [wma_tools](https://github.com/DanNBullock/wma_tools), with [vistasoft](https://github.com/vistalab/vistasoft) and [SPM](https://www.fil.ion.ucl.ac.uk/spm/software/) installed. Its documentation describes how to use it.

### Sample data

Explore [projects](https://brainlife.io/projects) on brainlife.io to find projects that contain [parcellations](https://brainlife.io/datatype/5c1a7489f9109beac4a88a1f).

## Outputs

```text
rois/
└── rois/
    ├── ROI1.nii.gz   ROI for the first line of roiPairs
    ├── ROI2.nii.gz   ROI for the second line
    └── …
```

## Troubleshooting

- **`didn't produce any rois`**: no ROI files were written. Check that the indices in `roiPairs` exist in the parcellation.
- **Inflation fails**: `smoothKernel` must be 0, 1 or an odd integer.

## Citing

> [!TIP]
> If you use this App in your work, please cite the App, the software it builds on and brainlife.io (below). GitHub's **Cite this repository** button gives the App citation in APA and BibTeX.

- Avesani P, McPherson B, Hayashi S, et al. The open diffusion data derivatives, brain data upcycling via integrated publishing of derivatives and reproducible open cloud services. *Scientific Data* 6:69 (2019). [doi:10.1038/s41597-019-0073-y](https://doi.org/10.1038/s41597-019-0073-y)
- Generate ROIs from an atlas. brainlife.io App. [doi:10.25663/brainlife.app.140](https://doi.org/10.25663/brainlife.app.140)
- Bullock et al. Associative white matter connecting the dorsal and ventral posterior human cortex. *Brain Structure and Function* 224:2631-2660 (2019). [doi:10.1007/s00429-019-01907-8](https://doi.org/10.1007/s00429-019-01907-8)
- Hayashi S, Caron BA, Heinsfeld AS, et al. brainlife.io: a decentralized and open-source cloud platform to support neuroscience research. *Nature Methods* 21:809–813 (2024). [doi:10.1038/s41592-024-02237-2](https://doi.org/10.1038/s41592-024-02237-2)

## Authors

- Daniel Bullock
- Franco Pestilli (project director)

**Contributors:** Soichi Hayashi

## Funding

[![Wellcome-226486/Z/22/Z](https://img.shields.io/badge/Wellcome-226486%2FZ%2F22%2FZ-purple.svg)](https://wellcome.org/)
[![NIH-NINDS-U24NS140384](https://img.shields.io/badge/NIH_NINDS-U24NS140384-green.svg)](https://reporter.nih.gov/project-details/11261037)
[![NIH-NINDS-UM1NS132207](https://img.shields.io/badge/NIH_NINDS-UM1NS132207-green.svg)](https://reporter.nih.gov/project-details/11411328)
[![NIH-NIBIB-R01EB029272](https://img.shields.io/badge/NIH_NIBIB-R01EB029272-green.svg)](https://reporter.nih.gov/project-details/10262925)
[![NIH-NIBIB-R01EB030896](https://img.shields.io/badge/NIH_NIBIB-R01EB030896-green.svg)](https://reporter.nih.gov/project-details/10428625)
[![NIH-NIMH-R01MH126699](https://img.shields.io/badge/NIH_NIMH-R01MH126699-green.svg)](https://reporter.nih.gov/project-details/10460628)
[![NIH-NIDCD-R01DC020088](https://img.shields.io/badge/NIH_NIDCD-R01DC020088-green.svg)](https://reporter.nih.gov/project-details/11140284)
[![NSF-BCS-1734853](https://img.shields.io/badge/NSF_BCS-1734853-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1734853)
[![NSF-BCS-2203524](https://img.shields.io/badge/NSF_BCS-2203524-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2203524)
[![NSF-IIS-1636893](https://img.shields.io/badge/NSF_IIS-1636893-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1636893)
[![NSF-OAC-2148729](https://img.shields.io/badge/NSF_OAC-2148729-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2148729)
[![NSF-IIS-1912270](https://img.shields.io/badge/NSF_IIS-1912270-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1912270)
[![NSF-IIS-2148700](https://img.shields.io/badge/NSF_IIS-2148700-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2148700)
[![NSF-OAC-1916518](https://img.shields.io/badge/NSF_OAC-1916518-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1916518)

<sub>brainlife.io is publicly funded, and acknowledging that support helps keep it running. This App was developed with support from Wellcome, the U.S. National Institutes of Health (including the BRAIN Initiative BRAIN CONNECTS program: The Axonal Projectome EXchange, U24NS140384, and the Center for Mesoscale Connectomics, UM1NS132207) and the U.S. National Science Foundation.</sub>

## License

MIT.
