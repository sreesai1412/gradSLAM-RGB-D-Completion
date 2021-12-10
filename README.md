# gradSLAM-RGB-D-Completion

[gradSLAM] (https://gradslam.github.io/) is a fully differentiable dense SLAM framework, i.e., it allows for gradient flow from the outputs of an RGB-D SLAM system (trajectory, 3D map of the environment) all the way through to its inputs (color and depth images). This project is based on leveraging pixel level gradient information backpropagated by gradSLAM to recover missing color and depth observations from an image sequence.

We are given a sequence of four RGB-D images {(I​1, ​D​1)​, (I​2, ​D​2)​, (I​3,​ D​3)​, (I​4, ​D​4)​} along with the camera intrinsics and the ground-truth extrinsics (i.e., camera poses). The fourth RGB-D pair (I​4,​ D​4)​ is corrupted by adding noise or replacing it entirely with a constant value. The goal is to leverage gradients from gradSLAM to reconstruct (I​4,​ D​4)​ without directly accessing this RGB-D pair. The process can be visualized as follows:

<img src="https://github.com/sreesai1412/gradSLAM-RGB-D-Completion/blob/main/assets/schema.png" />

## Experiments & Results
Various initialization experiments are performed to gain insights on the optimization performance for both RGB and 16-bit depth images. First, we consider the first 4 RGB-D images from the ICL-NUIM dataset and obtain the ground truth 3D scene reconstruction as a pointcloud using PointFusion. Then, by corrupting the fourth RGB-D image in different ways various initialization experiments are performed to gain insights on the optimization performance for both RGB and 16-bit depth images. \\
Across all the experiments, two loss terms are used for the optimization. For optimization of depth images is performed using Chamfer distance between the ground truth pointcloud and the corrupted pointcloud (obtained using the the first 3 ground truth RGB-D image and the corrupted fourth image).
For RGB optimization, the nearest neighbours point (in the ground truth pointcloud) of each point in the corrupted pointcloud is found out and absolute error between their colors is used as the loss.
The experimental results and insights gathered from the project show us that gradient information from gradSLAM is spatially rich. 

### 1) Constant Value RGB-D Image

In this experiment, the fourth RGB-D image is corrupted by intializing with a constant value. Here, the RGB image is intialized with the color 'grey' and the 16-bit depth image is initialized with a value of 10,015. The results of the optimization process is shown below:

<img src="https://github.com/sreesai1412/gradSLAM-RGB-D-Completion/blob/main/assets/constant.png" />

### 2) Slight Noise Addition

In this experiment, the fourth RGB-D image is corrupted adding gaussian noise to the ground truth RGB-D image.

<img src="https://github.com/sreesai1412/gradSLAM-RGB-D-Completion/blob/main/assets/uniform.png" />

### 3) Uniform Noise Image

In this experiment, the fourth RGB-D image is corrupted by intializing with a uniform gaussian noise image.

<img src="https://github.com/sreesai1412/gradSLAM-RGB-D-Completion/blob/main/assets/slightnoise.png" />

## Using PyTorch-3D for RGB recovery

We render the corrupted as well as the ground-truth point clouds using a differentiable point cloud renderer provided by [PyTorch3D](https://pytorch3d.org) and find the [Perceptual Similarity](https://github.com/richzhang/PerceptualSimilarity) metric between the 2 RGB images obtained upon rendering. We use this loss to optimize the corrupted fourth RGB image.
In this following visualization, the fourth RGB-D image is corrupted by intializing with a uniform gaussian noise image.

## Running Experiments

### 1) RGB-D Optimization Experiments

For running the optimization experiments, please look at the [gradSLAM_Optimization.ipynb](https://github.com/sreesai1412/gradSLAM-RGB-D-Completion/blob/main/gradSLAM_Optimization.ipynb)
``` 
!pip install -q 'git+https://github.com/gradslam/gradslam.git' 
!git clone https://github.com/sreesai1412/gradSLAM-RGB-D-Completion.git
%cd /content/fradSLAM-RGB-D-Completion
!python main.py --save_dir='/content/' --experiment
```
Here, the --experiment flag can be 'constant_value', 'uniform_noise' or 'slight_noise'

### 2) PyTorch 3D Experiment

For running the PyTorch 3D experiment experiment, please look at the [gradSLAM_PyTorch3D.ipynb](https://github.com/sreesai1412/gradSLAM-RGB-D-Completion/blob/main/gradSLAM_PyTorch3D.ipynb)

## References

1) [gradSLAM Documentation](https://github.com/gradslam/gradslam)
2) [Chamfer Distance](https://github.com/krrish94/chamferdist)
3) [PyTorch3D](https://pytorch3d.org)
4) [Perceptual Similarity](https://github.com/richzhang/PerceptualSimilarity)



