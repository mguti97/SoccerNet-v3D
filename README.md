# SoccerNet-v3D: Leveraging Sports Broadcast Replays for 3D Scene Understanding

<div align="center">

[![arXiv](https://img.shields.io/badge/arXiv-2504.10106-b31b1b.svg?style=for-the-badge)](https://arxiv.org/abs/2504.10106)

![](/figures/pipeline.gif)
</div>

## Contents
- [Abstract](#abstract)
- [Annotations](#annotations)
- [Weights](#weights)
- [Citation](#citation)


## Abstract
Sports video analysis is a key domain in computer vision,
enabling detailed spatial understanding through multi-view
correspondences. In this work, we introduce SoccerNetv3D
and ISSIA-3D, two enhanced and scalable datasets
designed for 3D scene understanding in soccer broadcast
analysis. These datasets extend SoccerNet-v3 and ISSIA
by incorporating field-line-based camera calibration and
multi-view synchronization, enabling 3D object localization
through triangulation. We propose a monocular 3D ball localization
task built upon the triangulation of ground-truth
2D ball annotations, along with several calibration and reprojection
metrics to assess annotation quality on demand.
Additionally, we present a single-image 3D ball localization
method as a baseline, leveraging camera calibration and
ball size priors to estimate the ball’s position from a monocular
viewpoint. To further refine 2D annotations, we introduce
a bounding box optimization technique that ensures
alignment with the 3D scene representation. Our proposed
datasets establish new benchmarks for 3D soccer scene understanding,
enhancing both spatial and temporal analysis
in sports analytics.

<p align="center"><img src=/figures/pipeline-1.png  width=90% height=90%></p>
<strong>SoccerNet-v3D dataset generation pipeline</strong>. A main camera frame is paired with its corresponding synchronized replay frames,
where blue dots indicate the original SoccerNet-v3 field-line annotations. The PnLCalib calibration pipeline is used to recover
camera parameters {K,R, t}. Calibration quality is assessed using JaCγ, with a threshold of JaC0.5% = 0.75 to determine whether
frames qualify as part of the multi-view system. Red lines represent the field projection obtained from the estimated calibration. Finally,
2D ball annotations are fused through triangulation to estimate 3D ball positions, while original bounding boxes are optimized to ensure
consistency with the 3D scene, with the original SoccerNet-v3 and optimized bounding boxes represented in red and blue, respectively.


## Annotations
SoccerNet-v3D and ISSIA-3D datasets are built as an extension of the original [SoccerNet-v3](https://github.com/SoccerNet/SoccerNet-v3) and [ISSIA-Soccer](https://pspagnolo.jimdofree.com/download/) datasets, respectively.


### SoccerNet-v3D
Calibration annotations for [SoccerNet-v3D](https://github.com/mguti97/SoccerNet-v3D/releases/download/v1.0.0/SoccerNet-v3D.zip) follow the same folder structure as the original SoccerNet-v3. Therefore, folders can be merged so that each match contains a single generated <strong>Labels-v3D.json</strong> file. The calibration file is structured as follows (only generated information not included in the original SoccerNet-v3 annotations is displayed):

```
Labels-v3D.json
  |-- GameMetadata
  |      |-- reconstructions # list of actions with more than 1 calibrated frames
  |-- actions
  |      |-- "0.png"
  |      |      |-- imageMetadata
  |      |      |      |-- calibrated_replays # i.e. ["0_0.png", "0_1.png"] calibrated replays associated with the action frame
  |      |      |-- calibration # camera calibration parameters following SoccerNet-calibration format
  |      |      |      |-- pan_degrees
  |      |      |      |-- tilt_degrees
  |      |      |      |-- roll_degrees
  |      |      |      |-- x_focal_length
  |      |      |      |-- y_focal_length
  |      |      |      |-- principal_point
  |      |      |      |-- position_meters
  |      |      |      |-- rotation_matrix
  |      |      |      |-- radial_distortion
  |      |      |      |-- tangential_distortion
  |      |      |      |-- thin_prism_distortion
  |      |      |-- JaC # i.e. Jaccard index for the calibration given different thresholds
  |      |      |      |-- JaC@0.005
  |      |      |      |-- JaC@0.01
  |      |      |      |-- JaC@0.02
  |-- replays
  |      |-- "0_0.png" #same strcuture as action frames

```

Annotations containing camera calibration and ball position information (both 2D and 3D) can be downloaded in [CSV](https://github.com/mguti97/SoccerNet-v3D/releases/download/v1.0.0/SNv3D.csv) format, with the following columns:
- `league`: league name  
- `season`: season year  
- `match`: match identifier  
- `main_action`: main-camera shot ID  
- `action`: boolean flag indicating if the frame corresponds to a main-camera shot  
- `replay`: replay number; NaN if it’s an action frame  
- `img_w` / `img_h`: image width and height  
- `is_ball`: boolean flag indicating if a ball annotation exists in the frame  
- `ball_bbox`: ball bounding box; NaN if no ball annotation is present  
- `calibration`: camera calibration dictionary in SoccerNet-calibration format  
- `JaC@0.005`, `JaC@0.01`, `JaC@0.02`: Jaccard index at different thresholds  
- `ball_3D`: 3D ball position in meters (via triangulation)  
- `rep_error`: 3D ball reprojection error in image space (pixels)  
- `optimized_error`: projection error after bounding box optimization (meters)  
- `optimized_d`: optimized ball diameter in image space (pixels)  
- `set`: `"train"` / `"test"` flag for ball detector training


### ISSIA-3D

Camera calibration parameters for the six cameras in the ISSIA-Soccer dataset are provided in the SoccerNet-calibration format and can be downloaded in [JSON](https://github.com/mguti97/SoccerNet-v3D/releases/download/v1.0.0/issia_calibration.json) format.

**IMPORTANT:** Cameras 2 and 6 have their downloadable footage horizontally flipped. Calibration and player/ball annotation files are based on the original (unflipped) images. Please horizontally flip the images to match the original footage orientation.

Annotations containing ball position information (both 2D and 3D) can be downloaded in [CSV](https://github.com/mguti97/SoccerNet-v3D/releases/download/v1.0.0/ISSIA-3D.csv) format, with the following columns:

- `x_cam`: x-coordinate of the ball position in image space (pixels)  
- `y_cam`: y-coordinate of the ball position in image space (pixels)  
- `num_cameras`: number of cameras with a ball annotation in the same frame (*)
- `ball_3D`: 3D ball position in meters (via triangulation) (*)
- `err_cam`: 3D ball reprojection error in image space (pixels)  
- `cam_list`: list of cameras used in the triangulation process (*)
- `opt_d_cam`: optimized ball diameter in image space (pixels)  
- `opt_e_cam`: projection error after bounding box optimization (meters)  

**Note:** All six cameras are included in the CSV file. The specific camera index (from 1 to 6) is appended to each camera-related column name (e.g., `x_cam1`, `y_cam1`, `err_cam1`), except for the general columns (*).


## Weights
Download the ball object detector weights for the different configurations. Our models are pre-trained on the SoccerNet distribution and further fine-tuned on the SoccerNet and ISSIA datasets using optimized bounding boxes for enhanced performance.
The model architecture and dependencies for YOLOv11 can be obtained from the [original source](https://github.com/ultralytics/ultralytics).

| Trained | Finetuned | Link |
| :-- | :-: | :-: |
|SoccerNet-v3| X | [**yolo-sn-ball.pt**](https://github.com/mguti97/SoccerNet-v3D/releases/download/v1.0.0/yolo-sn-ball.pt) |
|SoccerNet-v3| SoccerNet-v3D optimized boxes | [**yolo-sn-ball-opt.pt**](https://github.com/mguti97/SoccerNet-v3D/releases/download/v1.0.0/yolo-sn-ball-opt.pt)  |
|SoccerNet-v3| ISSIA-v3D optimized boxes | [**yolo-issia-ball-opt.pt**](https://github.com/mguti97/SoccerNet-v3D/releases/download/v1.0.0/yolo-issia-ball-opt.pt)  |

The list of images and annotations used in the train/val and test sets for the SoccerNet distribution can be found in the [`SNv3D-train.txt`](https://github.com/mguti97/SoccerNet-v3D/releases/download/v1.0.0/SNv3D-train.txt) and [`SNv3D-test.txt`](https://github.com/mguti97/SoccerNet-v3D/releases/download/v1.0.0/SNv3D-test.txt) files, respectively.

For the ISSIA distribution, the train/val split was performed using frames from cameras 3 to 6, while the test set consists of frames from cameras 1 and 2.



## Citation
```shell
@article{gutiérrezpérez2025soccernetv3dleveragingsportsbroadcast,
        title={SoccerNet-v3D: Leveraging Sports Broadcast Replays for 3D Scene Understanding}, 
        author={Marc Gutiérrez-Pérez and Antonio Agudo},
        year={2025},
        eprint={2504.10106},
        archivePrefix={arXiv},
        primaryClass={cs.CV},
        url={https://arxiv.org/abs/2504.10106}, 
}
```

