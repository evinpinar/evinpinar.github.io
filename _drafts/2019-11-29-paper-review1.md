---
layout: post
title: Paper Analysis on Monocular Depth Prediction for Autonomous Driving
categories: [AI, CV]
comments: true
---

In this blog post, I will review the paper [Depth Prediction Without the Sensors: Leveraging Structure for Unsupervised Learning from Monocular Videos](https://arxiv.org/abs/1811.06152) published in AAAI 2019 {% cite casser2019struct2depth %}. After briefly introducing the topic and the relevant concepts, I will explain the method in my own words. Then we will discuss the results and future works.  <a name="abcde"> [1] </a>

# Introduction

Depth prediction is a fundamental task for us to perceive the 3D environment around us. Through depth inference, we can grab the objects, move around and perform our daily tasks. To be able to create autonomous objects that can perform similar tasks to people, we should model this 3D perception. Autonomous cars should detect the other cars and pedestrians, and plan their trajectory in 3D environment. Likewise, robots need to move in their environment, grab things, carry to some distance and replace them. 

<img src="/images/paper_review1/kitti_output.gif">

Currently, depth estimation relies on sensors such as LIDAR and radar or RGBD cameras. However, there are two problems in this setting. First, these sensors return sparse inputs, especially perform poorly on edges, far points and uncommon surfaces. Second, they are extremely expensive to allow mass production. For these reasons, it is highly desirable to be able to estimate depth without external sensors, and only with an ordinary RGB camera. 

> "Can people perform depth inference perfectly with only one eye?"

Some believe that our depth prediction relies on perfectly calibrated two eyes, yet some others think that the second eye exists only for backup and does not effect our depth understanding.

Following this question, the research in depth estimation from RGB images is divided into two: **stereo** setting and the **monocular** setting. By stereo, I mean either images from stereo cameras, or the consecutive frames of videos taken with a single camera. By monocular, a single image without any previous or next similar frame. Also, the stereo setting inherently creates further opportunities in **unsupervised (self-supervised) learning**. Practically, self-supervision is very useful since we do not need any ground truth annotations!  


## Concepts

There are some ideas and keywords that occur in the literature that we should be aware of first. These concepts would be familiar to you if you have taken a Multi-View Geometry course. Otherwise, I will try to explain in very simple and clear way. 

<p align="left" style="font-size:110%; font-weight:bold;"> Rigid Body Motion</p>

Motion in 3D space is formalised by Euclid and the "Euclidean coordinates" usually refers to 3D coordinates that concern us in real life. Here, we can define the motions of rigid objects as rotation and translation.

<img src="/images/paper_review1/se3.png" height="75">

We can apply this matrix to any 3D point to get the new coordinates of point after a motion.

<img src="/images/paper_review1/rbd1.png" height="40">

<img src="/images/paper_review1/xvec.png" height="25">


<p align="left" style="font-size:110%; font-weight:bold;">Perspective Projection</p>

The study of projecting the 3D points to 2D images taken with a camera is "perspective projection". For such a pin-hole thin lense camera model with a focal length f:

<img src="/images/paper_review1/focal.png" height="250">

We can retrieve x and y image coordinates by:

<img src="/images/paper_review1/focal2.png" height="50">

In vector form, it is same with:

<img src="/images/paper_review1/focal3.png" height="60">

And finally, the standardized notation in homogeneous coordinates is: 

<img src="/images/paper_review1/focal4.png" height="100">


<p align="left" style="font-size:110%; font-weight:bold;">Structure vs Motion</p>

Here, **structure** refers to estimating the real world coordinates of points, and specifically the depth values. It is about recovering the geometry. Wheras **motion** refers to recovering rigid body motion (transformation) of these points. To be have a full understanding of 3D environment, we should estimate both. If we know one, the estimation of the other becomes easier.

<p align="left" style="font-size:110%; font-weight:bold;">Odometry</p>


It is the study of estimating motion of an object. **Ego-motion** refers to estimating the camera's 3D motion.

<p align="left" style="font-size:110%; font-weight:bold;">Image warping</p>

It literally means moving the pixels of images. For example, we can multiply the x coordinate pixels with two, and the image will look twice bigger horizontally. In our case, we are interested in moving the pixels according to a known transformation matrix. Let’s say we have a point pt with the depth value D(pt). We can apply a transformation to that pixel and can find it’s new location with the following formula:

<img src="/images/paper_review1/warp.png" height="60">

Or if we know the new location of the point, but do not know the depth, we can infer it’s depth value with the same formula. Likewise, if we don’t know the transformation but have depth values, T can be directly calculated.

**Assumptions:** 

1. *Photo-consistency*: A 3D voxel should have same colors on different images taken from different angles. With this assumption, we can take two images of the same scene, and find the transformation between images. 

2. *Lambertian surface*: It is the ideal matte surface where the brightness is same from different angles. The shiny, metal or glass surfaces in the scene are non-Lambertian.

# Previous Works

<p align="left" style="font-size:110%; font-weight:bold;">1. Supervised monocular</p>
Depth estimation from an RGB image is an ill-posed problem which cannot be solved by having assumptions and priors about the real world. For that reason, it is particularly suitable for neural networks which can capture the parameters and the internal priors in the images. First neural succesful model was introduced by Eigen {% cite eigen2014 %}. Then, Laina used a Resnet model and pointed the long-tailed distribution of depth values in scene {% cite laina2016deeper%}.


<p align="left" style="font-size:110%; font-weight:bold;">2. Unsupervised stereo</p>

When there are no depth ground truths but two images taken from a stereo camera, as I have told before, if we know the transformation between the cameras, we can estimate the depth values of the points. This is called stereopsis, or left-right consistency. Godard proposed a model based on this principle which can be seen below {% cite monodepth17%}:

<img src="/images/paper_review1/godard.png" height="250">

Ummenhofer predicted optical flow and normals along the depth values which work as complementary tasks {% cite ummenhofer_demon:_2017%}. Zhan worked on feature levels {% cite Zhan_2018_CVPR%}. 


<p align="left" style="font-size:110%; font-weight:bold;">3. Unsupervised monocular</p>

When there are no stereo cameras but consecutive RGB frames, we can use these frames as stereo input. Garg used such videos with the known transformations between frames {% cite garg2016unsupervised%}. Take a look at their model:


<img src="/images/paper_review1/garg.png" height="250">

They estimate the depth values of the left image, and apply warping to reconstruct the right image. Then they use photometric consistency loss to train the network.

Zhou et al. improved this model by estimating the transformation parameters as well {% cite zhou2017unsupervised %}. I will talk about their model more in detail.

<p align="left" style="font-size:110%; font-weight:bold;">4. Optical flow in dynamic environment</p>

Optical flow is the 2D motion field between two images. Models of Yang et al. and Yin et al. are based on predicting optical flows instead of motion parameters {% cite yang yin %}. 

# Method

<p align="left" style="font-size:110%; font-weight:bold;">Problem Setup</p>

Our problem is estimating the structure and motion together in an unsupervised way. Hence, we wish to estimate depth values, along with the transformation matrices between different images. 

Zhou et al. proposed two neural networks for estimating these values separately{% cite zhou2017unsupervised %}. Namely, the first autoencoder takes an RGB image as input, and predicts the depth map by regression. The second network takes two images, and predicts the transformation parameters between these images (rotation and translation, 6 Degrees-Of-Freedom). Their network is:

<img src="/images/paper_review1/zhou.png" height="250">

> "How can we train networks to predict depth and transformation parameters correctly?"

Getting back to the concepts I have introduced, photo-consistency assumes that the objects (3D points) in the environment should have same color values on different images. To check that, we can warp the first image to the second one by the estimated parameters, and check if it has same color values with the target (ground truth) second image which can be seen below:

<img src="/images/paper_review1/zhou_vs.png" height="250">

<img src="/images/paper_review1/Lvs.png" height="50">

Here T describes the motion of the camera, and assumes that no other object moves in the space. There should not be major occlusions/disocclusions. Hence, dynamicity is minimum or none in the scene. Also the surfaces should be Lambertian.

<p align="left" style="font-size:110%; font-weight:bold;">Contributions of the Paper</p>

The main contributions of the paper are:
1. To improve Zhou et al's model for dynamic environments because real driving setting is highly dynamic with all the cars and objects moving around.
2. To overcome domain adaptation problems particularly for monocular depth estimation. 

<img src="/images/paper_review1/network.png" height="250">


<p align="left" style="font-size:110%; font-weight:bold;">Baseline Model</p>

Author’s use the same neural network from Zhou’s, with these loss functions:
1.*L_reconstruction*: Error between the ground truth frame and the synthesized view.

2.*L_SSIM*: It was introduced by Wang et al. as another metric to compare images{% cite wang%}. Instead of errors between pixels, it compares the luminance, contrast and brightness of images through following formula:

<img src="/images/paper_review1/SSIM.png" height="70">

3.*L_smoothness*: It is for further depth regularization. It is similar to total variation, but encourages depth map to have smooth edges on matching edges from RGB image. 

<img src="/images/paper_review1/Lsm.png" height="70">

Here, inputs are three consecutive frames 1-2-3 and the warpings are done between 1-2 and 2-3. They choose the one with the minimum error as suggested by Godard {% cite monodepth17%} to prevent any major occlusions that might occur and create huge unwanted errors:

<img src="/images/paper_review1/Lrec.png" height="40">


<p align="left" style="font-size:110%; font-weight:bold;">Motion Model</p>

Authors propose to estimate motion of each object in the scene separately, and combine them to create the final view. To do that, they precompute binary masks of objects through Mask R-CNN and apply to the scenes (shown as elementwise production in formulas). Then they:

1.Remove all objects from scene to have a static background to estimate ego-motion. We are left with background scenes without any moving objects.
<img src="/images/paper_review1/layout.png" height="35">
2.Apply estimated ego-motion to all frames to remove the effect of ego-motion.

<img src="/images/paper_review1/egomotion.png" height="30"> 

3.Then for each object, estimate it’s motion separately. (Input is the image where only the object is seen, other pixels are black.)

<img src="/images/paper_review1/objectmotion.png" height="80">
4.Reconstruct the new frame for each object and background by applying the masks.

<img src="/images/paper_review1/combine.png" height="90">

<p align="left" style="font-size:110%; font-weight:bold;">Object Size Constraint</p>

A common issue in these models is the *infinite-depth degeneracy*. When an object is moving at the same speed with the camera, the networks estimates depth values to the object. Because normally, that object should have some motion. If the motion is not observable, it must be very far away and we cannot perceive such a motion.

Another issue in monocular depth prediction is sometimes to estimate ridiculous depth values because the average shapes/heights of the objects are unknown. An object might be regarded as too close to the camera with a small motion, or far away from camera with a small motion.

Authors propose using some weak priors to lead meaningful depths. If an average height of the object is known, depth can be calculated directly:

<img src="/images/paper_review1/weakp.png" height="200">

<img src="/images/paper_review1/weakd.png" height="30">

Where h is the pixelwise height of the object and f focal length of camera. Ideally, this calculated depth should be same with the networks’ depth estimation for that object. Author’s model use differentiable weak priors for each different object category. 

<img src="/images/paper_review1/Lsc.png" height="80">


<p align="left" style="font-size:110%; font-weight:bold;">Online Refinement for Domain Transfer</p>

Finally, when a model trained on KITTI dataset is tested on a different driving dataset, this new dataset is regarded as a different domain because there are some inconsistencies:
1. Global scales between them do not match, they are taken with different cameras, different angles/heights.
2. Temporal shifts between frames may mismatch due to fps settings. 

To alleviate this, authors propose using the test data to train the model on the fly. Hence, each test data is iterated on the model for 20 times and model adapts itself for the new dataset. 


# Experiments

Authors run their experiments on these datasets:
1. KITTI (~40K images total, LIDAR depth sensor)
2. Cityscapes (~4K images especially high dynamicity)
3. Fetch Indoor Navigation (~1.5K images, collected from a robot in indoor environment, used for online refinement )

And they qualitatively evaluate the results in:

1.Depth metrics:
<img src="/images/paper_review1/depth_metrics.png" height="80">

2.Odometry metric (Absolute Trajectory Error)

Their depth and odometry results for KITTI establish SOTA according to these tables from paper:

<img src="/images/paper_review1/kittidepth.png" height="220">

<img src="/images/paper_review1/kittiodo.png" height="250">

The depth results especially show improvements on infinite-depth degeneracy problem as can be seen in the images below:

<img src="/images/paper_review1/infin.png" height="300">

And they applied online refinement on a Cityspaces trained model with Indoor Fetch dataset. Their motion model show some improvement on the baseline, but the refinement model makes it much better qualitatively. Also the depth maps become better as seen: 

<img src="/images/paper_review1/refin1.png" height="250">

<img src="/images/paper_review1/refin2.png" height="100">

# Discussion

As future work, authors suggest applying full 3D reconstruction on scenes to be able to use 3D priors. Also, using longer and different sequences for online refinement to understand the hidden mechanism seems like another interesting direction. 
Zhou pointed in their paper to make this process independent from camera intrinsics, to be able to train the model on any input video. 

Personally, I believe that this paper tackles a very relevant and realistic issue in odometry/depth estimation. Objects in real life are not static, and everything moves and changes!
* I find their masking method intuitive. Yet, training relies on pre-trained masks and it is questionable whether the method is fully unsupervised.
* Injecting weak priors is very creative and sensible. I think such priors should be investigated more and evaluated in detail. Currently, the paper does not provide any evaluations on that. 
* I think online refinement does not sound like a new method, but it is definitely interesting in self supervised depth estimation setting. How does model modify itself to new data domain, which parts get updated, and do they point to any specifics like camera intrinsics, scale vs?

Some open questions arose are:
* How to evaluate the object motions? Maybe a simulator can be used to create a dataset where all the object motion ground-truths are known. 
* Are there any other cues/assumptions in object motions, similar to the weak priors? 
* Do pose and depth models train in a way that they depend on each other, or is it a fully independent case? For example, what happens if we use a single camera but in two settings: one by placing the camera on top of the car, one on the hood of the car. Can we use the same depth networks but train for new poses? 


<p align="left" style="font-size:110%; font-weight:bold;">My run</p>

Writers provide their model network in Tensorflow code [struct2depth](https://github.com/tensorflow/models/tree/master/research/struct2depth). I have run their code both on a KITTI sequence and a random driving video from Youtube. To prepare the object instance labels, I used matterport's [Mask R-CNN implementation](https://github.com/matterport/Mask_RCNN). As preparing the data was a little tedious, I provide the jupyter lab files that I have used [here]().

<img src="/images/paper_review1/melbourne.gif">

<img src="/images/paper_review1/kitti_output.gif">


To sum up, I think this paper is a good attempt to alleviate the unrealistic static scene assumption and it supports flexible environments with self-supervised learning for autonomous driving. I believe there are many questions and problems in this area that can be tackled. 

<sub><sup>Here is a link for [presentation slides](https://drive.google.com/file/d/14oNrfLpiwHwWuJC5wNM-7XjZzmFoCYqM/view?usp=sharing) of this blog post.<sub><sup>

[Burada yazilar var vs](#abcde)

<p align="left" style="font-size:90%; font-weight:bold;">References</p>
{% bibliography --file references%}


