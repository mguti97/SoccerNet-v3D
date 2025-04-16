# SoccerNet-v3D: Leveraging Sports Broadcast Replays for 3D Scene Understanding

<div align="center">

[![arXiv](https://img.shields.io/badge/arXiv-2504.10106-b31b1b.svg?style=for-the-badge)](https://arxiv.org/abs/2504.10106)

![](/figures/pipeline.gif)
</div>

## Contents
- [Abstract](#abstract)
- [Annotations](#annotations)


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
