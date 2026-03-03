# Custom Fork Instructions
This fork was made to run LINGO on a custom dataset like ScanNet. 

- Follow the instructions in the original README to install the dependencies and download the dataset. Please also make sure to install the correct version of blender and the SMPL add on.
- Make sure you can run the visualisation for 'vis.blend' dataset. Make sure to run blender in the root of the repository to ensure paths work correctly.
- The structure for running LINGO -
    - The motion is divided into segments which require start and end goal markers.
    - To generate motion you can add input text, start and end goal markers in the `get_input` script within blender. After that run this script
    - By default this saves the input dictionary for 'demo-21' script within `results/inputs/demo-21.pkl`
    - After doing this, run `python sample_lingo.py` to generate motion. This saves the generated motiont to `results/motions/demo-21.pkl`. Run the `vis_output` script in blender to load the animations

```markdown
- **[NOTE: For custom datasets, look below, this is only for scenes from their dataset]** To run on other samples from the training set, use the following instructions & video as reference -
```
    - Load the `vis.blend` scene into blender
    - Select the mesh you want to use from `Scene_mesh` folder and add the .obj file for it to blender. 
    - The mesh will cointain the scan for the whole room, we need to pre-process it so that we can give goal markers correctly. Please note that LINGO doesn't directly use .obj files, rather needs a voxel grid, so any modifications we make here are only for visually providing goal markers correctly. The voxel grids are pre-defined for the training scenes, so you don't need to do this for them.
    - Copy the voxel grid from `dataset/Scene` to `dataset/Scene_vis` and rename for the selected scene. e.g - copy `dataset/Scene/060.npy` to `dataset/Scene_vis/demo-060.npy`
    - The LINGO scans are for the whole room. So we can slice them in half for easier visualisation, look at the video on how to do so.
    - Place goal markers in this environment and add segments. Remember to also change the file name in the `vis_output` script.
    - Run the sampling script with the extra argumnet `python sample_lingo.py test_setting=demo-[scene name]`. E.g - For 060 `python sample_lingo.py test_setting=demo-060` 
    



# Autonomous Character-Scene Interaction Synthesis from Text Instruction

<center><img src="assets/teaser.png" alt="HSI motion synthesis" style="zoom:40%;" /></center>

#### This is the code repository of **Autonomous Character-Scene Interaction Synthesis from Text Instruction** at **SIGGRAPH Asia 2024** 
#### [arXiv](https://arxiv.org/abs/2410.03187) | [Project Page](https://lingomotions.com/) | [Dataset](https://drive.google.com/file/d/1RadpLt-woPvsIGk9yDW7yX7br3sRO-zi/view?usp=sharing) | [Demo](https://www.youtube.com/watch?v=-Uz6lGLdTy4)


# LINGO Dataset

Please download the LINGO dataset from [Google Drive](https://drive.google.com/drive/folders/1oJ-dnrMahyxoVQxDVAYdPe3MC17kUFAa?usp=sharing). The content inside the download link will be continuously updated to ensure you have access to the most recent data.

Explanation of the files and folders of the LINGO dataset:

- **Scene (folder):** This folder contains the occupancy grid for indoor scenes in LINGO dataset, indicated by each file name. The scenes are mirrored for augmentation.
- **Scene_vis (folder):** This folder contains the occupancy grid for another set of indoor scenes, which we used to test our model and visualize the motions.
- **language_motion_dict (folder):** This folder contains wrapped infomation of each motion segment we used to train our model.
- **human_pose.npy:** This file contains a (N x 63) array, where each row corresponds to the 63-dimensional SMPL-X body_pose parameter of one frame of MoCap data. The data is a concatenation of all motion segments.
- **human_orient.npy:** This file contains a (N x 3) array corresponding to the global_orient parameter of SMPL-X.
- **transl_aligned.npy:** This file contains a (N x 3) array corresponding to the transl parameter of SMPL-X.
- **human_joints_aligned.npy:** This file contains a (N x 28 x 3) array corresponding to the selected joints 3D location (y-up) of SMPL-X.
- **scene_name.pkl:** This file contains a (N, ) list corresponding to the scene name of each frame.
- **start_idx.npy:** This file contains a (M x 3) array corresponding to the start frame index of each motion segment.
- **end_idx.npy:** This file contains a (M x 3) array corresponding to the end frame index of each motion segment.
- **text_aug.pkl:** This file contains a (M, ) list corresponding to the text annotations of each motion segment.
- **left_hand_inter_frame.npy:** This file contains a (M, ) array stores frame IDs where left hand-object contact occurs. And it contains -1 values for motion segments with no left hand-object contact.
- **right_hand_inter_frame.npy:** This file contains a (M, ) array stores frame IDs where right hand-object contact occurs. And it contains -1 values for motion segments with no right hand-object contact.
- **clip_features.npy:** This file contains the preprocessed CLIP features of text annotations in LINGO dataset.
- **text2features_idx.pkl:** This file stores a dictionary that maps text annotations to their corresponding CLIP feature vectors.
- **norm_inter_and_loco__16frames.npy:** This file is a (2, 3) array containing the range of joint coordinates along x, y, and z axes, used for normalizing joint locations.

#### Note: N represents the total number of frames in the LINGO dataset, while M represents the number of motion segments. This dataset is provided in mirrored form.


# Human Motion Synthesis in Indoor Scenes

## Prerequisites

To run the code, you need to have the following installed:

- Python 3.8+
- Required Python packages (specified in `requirements.txt`)

## Installation

1. **Clone the Repository**:
    ```sh
    git clone git@github.com:mileret/lingo-release.git
    ```

2. **Download Checkpoints, Data, and SMPL-X Models**:
    - Download the necessary files and folders from [this link](https://drive.google.com/file/d/1L2V8RlPMAhWF93o_RpIznO_bacjSSLqu/view?usp=drive_link).
    - Extract `lingo_utils.zip`, and place the four files and folders (`dataset`, `ckpts`, `smpl_models`, `vis.blend`) at the root of the project directory.


3. **Install Python Packages**:
    ```sh
    pip install -r requirements.txt
    ```

4. **Install Blender**:
    - We use [Blender](https://www.blender.org/) for visualization of the result.
    - Please download Blender3.6 from its [official website](https://download.blender.org/release/Blender3.6/).
    - (Optional) Then, download [SMPL-X Blender Add-on](https://smpl-x.is.tue.mpg.de/download.php) and activate it in Blender.

## Inference and Visualization

1. **Get Model Input**:

    Open `vis.blend` with [Blender](https://www.blender.org/). Change the `text`, `start_location`, `end_goal` and `hand_goal`. Then run `get_input` in `vis.blend`.

2. **Inference**:

    To synthesis human motions using our model, run

    ```sh
    cd code
    python sample_lingo.py
    ```

3. **Visualization in Blender**:

    Run `vis_output` in `vis.blend`.

    The generated human motion will be displayed in Blender.


# Training
## Overview

This README provides instructions on setting up and training our model using the LINGO dataset.

## Prerequisites

Before you begin, make sure you have the following software installed:

```sh
pip install -r requirements.txt
```

## Model Training

Navigate to the `code` directory:

```bash
cd code
```

To start training the model, run the training script from the command line:

```bash
python train_lingo.py
```

The training script will automatically load the dataset, set up the model, and commence training sessions using the configurations in `./code/config` folder.


# Citation
```
@inproceedings{jiang2024autonomous,
  title={Autonomous character-scene interaction synthesis from text instruction},
  author={Jiang, Nan and He, Zimo and Wang, Zi and Li, Hongjie and Chen, Yixin and Huang, Siyuan and Zhu, Yixin},
  booktitle={SIGGRAPH Asia 2024 Conference Papers},
  pages={1--11},
  year={2024}
}
```
