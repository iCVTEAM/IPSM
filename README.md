# Code for How to Use Diffusion Priors under Sparse Views? (NeurIPS 2024)

## Installation

Ubuntu 22.04, CUDA 11.3, PyTorch 1.12.1

``````
conda env create --file environment.yaml
conda activate ipsm

pip install ./submodules/diff-gaussian-rasterization-confidence ./simple-knn
``````

## Pre-trained Models Preparation

```
mkdir pretrained_models
cd pretrained_models
```

Download [StableDiffusion-v1.5](https://huggingface.co/runwayml/stable-diffusion-v1-5), [StableDiffusionInpainting-v1.5](https://huggingface.co/runwayml/stable-diffusion-inpainting), [MiDaS](https://github.com/isl-org/MiDaS), [BLIP](https://huggingface.co/Salesforce/blip-image-captioning-base) to ```./pretrained_models/```. (NOTE: Stable Diffusion V1.5 and Stable Diffusion Inpainting V1.5 cannot be downloaded from the original repo, but the same weight can be obtained from other clone repo.)

## Data Preparation

### LLFF

1. Download LLFF from [the official download link](https://drive.google.com/file/d/11PhkBXZZNYTD2emdG1awALlhCnkq7aN-/view?usp=drive_link).

2. Run COLMAP to obtain initial point clouds with sparse views:

   ```
   python tools/colmap_llff.py
   ```

3. Randomly select one image from sparse views and run BLIP to obtain its blip-based text results:

    ```
    python ./scripts/script_for_blip.py
    ```

4. The data format is supposed to be:

    ```
    |- <scene>
        |- 3_views
        |- images
        |- images_4
        |- images_8
        |- sparse
        |- blip_rst.txt
        |- poses_bounds.npy
        |- ...
    ```

### DTU

1. Download DTU dataset

   - Download the DTU dataset "Rectified (123 GB)" from the [official website](https://roboimagedata.compute.dtu.dk/?page_id=36/), and extract it.
   - Download masks (used for evaluation only) from [this link](https://drive.google.com/drive/folders/1OEmJcbP0XUVfG647mdYWxEy9yOw7L_Si?usp=drive_link) (backed up from RegNeRF).

2. Preprocess following [DNGaussian](https://github.com/Fictionarry/DNGaussian)

   - Poses: following [gaussian-splatting](https://github.com/graphdeco-inria/gaussian-splatting), run `convert.py` to get the poses and the undistorted images by COLMAP.
   - Render Path: following [LLFF](https://github.com/Fyusion/LLFF) to get the `poses_bounds.npy` from the COLMAP data. (Optional)

3. Run COLMAP to obtain initial point clouds with sparse views:

   ```
   python tools/colmap_dtu.py
   ```

4. Randomly select one image from sparse views and run BLIP to obtain its blip-based text results:

    ```
    python blip_script.py
    ```

5. The data format is supposed to be:

    ```
    |- <scene>
        |- 3_views
        |- images
        |- images_2
        |- images_4
        |- images_8
        |- mask
        |- sparse
        |- blip_rst.txt
        |- poses_bounds.npy
        |- ...
    ```

## Training & Rendering & Evaluating

Train & Render & Evaluate IPSM-Gaussian on the LLFF dataset with 3 views:

```
python ./scripts/script_for_llff.py
```

Train & Render & Evaluate IPSM-Gaussian on the DTU dataset with 3 views:

```
python ./scripts/script_for_dtu.py
```

## Acknowledgement

This code is developed on [gaussian-splatting](https://github.com/graphdeco-inria/gaussian-splatting), [FSGS](https://github.com/VITA-Group/FSGS), and [DNGaussian](https://github.com/Fictionarry/DNGaussian). Thanks for these great projects!

## Citation

```
@inproceedings{
wang2024how,
title={How to Use Diffusion Priors under Sparse Views?},
author={Qisen Wang and Yifan Zhao and Jiawei Ma and Jia Li},
booktitle={The Thirty-eighth Annual Conference on Neural Information Processing Systems},
year={2024},
url={https://openreview.net/forum?id=i6BBclCymR}
}
```
