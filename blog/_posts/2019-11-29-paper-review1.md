---
layout: post
title: Paper Analysis on Monocular Depth Prediction for Autonomous Driving
categories: [AI, CV]
comments: true
---

In this blog post, I will review the paper [Depth Prediction Without the Sensors: Leveraging Structure for Unsupervised Learning from Monocular Videos](https://arxiv.org/abs/1811.06152) published in AAAI 2019 [[1]](#1). After briefly introducing the topic and relevant concepts, I will explain the method in my own words. Then we will discuss the results and future works. 

# Introduction

Depth prediction is a fundamental task in perceiving the 3D environment around us. Through depth inference, we can grab the objects, move around and perform our daily tasks. To be able to create autonomous objects that can perform similar tasks to humans, we should model this 3D perception. Autonomous cars should detect other cars and pedestrians on the road, and plan their trajectory in 3D environment. Likewise, robots need to move in their environment, grab things, carry them to some distance and replace them. Look at a KITTI sequence with depth predictions [[2]](#2):

![kitti](/images/paper_review1/kitti_output.gif)


Currently, depth estimation relies on LIDAR and radar sensors or RGBD cameras. However, there are two problems in this setting. First, these sensors return *sparse* inputs, especially performing poorly on edges, far points and uncommon surfaces. Second, they are extremely *expensive* to allow mass production. For these reasons, it is highly desirable to be able to estimate depth without external sensors and only with an ordinary RGB camera. 

> "Can people perform depth inference perfectly with only one eye?"

Some believe that our depth perception relies on perfectly calibrated two eyes, yet some others think that second eye exists only for backup and does not effect our depth understanding.

Following this question, the research in depth estimation from RGB images is divided into two: **stereo** setting and the **monocular** setting. By stereo, I mean either images from stereo cameras, or the consecutive frames of videos taken with a single camera. By monocular, I refer to a single image without any previous or next similar frames. Also, the stereo setting inherently creates further opportunities in **unsupervised (self-supervised) learning**. Practically, self-supervision is very useful since we do not need any ground truth annotations!  


## Concepts

There are some ideas and keywords that occur in the literature that we should be aware of first. These concepts would be familiar to you if you have taken a Multi-View Geometry course. Otherwise, I will try to explain in very simple and clear way. 

<p align="left" style="font-size:110%; font-weight:bold;"> Rigid Body Motion</p>

Motion in 3D space is formalised by Euclid and the "Euclidean coordinates" usually refer to 3D coordinates that concern us in real life. Here, we can define motions of rigid objects as rotation(3x3 matrix or 3 angles) and translation(3-dim vector). Then, the combination of these motions can be built in homogeneous coordinates in transformation matrix:

<img src="/images/paper_review1/se3.png" height="75">

We can apply this matrix to any 3D point to get the new coordinates of point after a motion.

<img src="/images/paper_review1/rbd1.png" height="40">

<img src="/images/paper_review1/xvec.png" height="25">


<p align="left" style="font-size:110%; font-weight:bold;">Perspective Projection</p>

The study of projecting 3D points to 2D images with a camera is "perspective projection". For a pin-hole thin lense camera with a focal length f:

<img src="/images/paper_review1/focal.png" height="250">

we can retrieve x and y image coordinates by simple geometry:

<img src="/images/paper_review1/focal2.png" height="50">

In vector form:

<img src="/images/paper_review1/focal3.png" height="60">

And finally, the standardized notation in homogeneous coordinates is: 

<img src="/images/paper_review1/focal4.png" height="100">

We can estimate the 2D coordinates from a known 3D point up to a scale λ which depends on the depth values.

<p align="left" style="font-size:110%; font-weight:bold;">Structure vs Motion</p>

Here, **structure** refers to estimating the real world coordinates of points, and specifically the depth values. It is about recovering the geometry. Whereas **motion** refers to recovering rigid body motion (transformation) of these points. To have a full understanding of 3D environment, we should estimate both. If we know one, estimation of the other becomes easier.

<p align="left" style="font-size:110%; font-weight:bold;">Odometry</p>


It is the study of estimating motion of an object. **Ego-motion** refers to estimating camera's 3D motion.

<p align="left" style="font-size:110%; font-weight:bold;">Image warping</p>

It literally means moving pixels of images. For example, we can multiply x coordinate with two, and the image will look twice as bigger horizontally. In our case, we are interested in moving pixels according to a known transformation matrix. Let’s say we have a point pt with the depth value D(pt). We can apply a transformation to that pixel and can find it’s new location with the following formula:

<img src="/images/paper_review1/warp.png" height="60">

Here, we first convert the pixel coordinates to camera coordinates by multiplying with inverse intrinsics matrix. Then, we retrieve the world coordinates by depth. After applying rigid body motion, we project the world coordinates back to pixel coordinates.

With this formula, we can infer any variable if we know the values of the others. For example, if we know the new location of the point (pt and ps), but do not know the depth (D(pt)), we can infer it’s depth value if we know T and K. Likewise, if we don’t know the transformation but have depth values, T can be calculated directly.

**Assumptions:** 

1. *Photo-consistency*: A 3D voxel should have same colors on different images taken from different angles. With this assumption, we can take two images of the same scene, and find the transformation between images. 

2. *Lambertian surface*: It is the ideal matte surface where the brightness is same from different angles. The shiny, metal or glass surfaces in the scene are non-Lambertian.

# Previous Works

<p align="left" style="font-size:110%; font-weight:bold;">1. Supervised monocular</p>
Depth estimation from an RGB image is an ill-posed problem which cannot be solved without having assumptions and priors about the real world. Neural networks are known to be good for capturing the internal and hidden features of the inputs by looking at the outputs. For that reason, monocular depth prediction is particularly suitable for neural networks which can capture such hidden assumptions and priors in the images. The first succesful neural model was introduced by Eigen [[4]](#4) and showed to be superior to the previous traditional approaches. Then, Laina used a ResNet model and pointed to the long-tailed distribution of depth values in scene [[5]](#5).


<p align="left" style="font-size:110%; font-weight:bold;">2. Unsupervised stereo</p>

When there are no ground truth depths but two images taken from a stereo camera, if we know the transformation between the cameras, we can estimate depth values of the points. This is called stereopsis, or left-right consistency. Godard proposed a model based on this principle which can be seen below [[6]](#6):

<img src="/images/paper_review1/godard.png" height="250">

They estimate the left and right depth maps from a given left RGB image. Then they warp them back to images and compare the pixels by photo-consistency assumption.

On the same line, Ummenhofer predicted optical flow and normals along the depth values for stereo images [[7]](#7). Such side tasks known to work in the similar direction with depth prediction and improve the results through multi-task learning. Moreover, Zhan worked on feature levels of the neural networks [[8]](#8). 


<p align="left" style="font-size:110%; font-weight:bold;">3. Unsupervised monocular</p>

When there are no stereo cameras but consecutive RGB frames, we can use these frames as stereo input. Garg used such videos with the known transformations between frames [[12]](#12). Take a look at their model:


<img src="/images/paper_review1/garg.png" height="250">

They estimate the depth values of a left image, and apply warping to reconstruct a right image. Then they use photometric consistency loss to train the network.

Zhou et al. improved this model by estimating the transformation parameters as well [[3]](#3). I will talk about their model later in more detail.

<p align="left" style="font-size:110%; font-weight:bold;">4. Optical flow in dynamic environment</p>

Optical flow is the 2D motion field between two images. Models of Yang et al. and Yin et al. are based on predicting optical flows instead of motion parameters [[9]](#9), [[10]](#10). 

# Method

<p align="left" style="font-size:110%; font-weight:bold;">Problem Setup</p>

Our problem is estimating the structure and motion together in an unsupervised way. Hence, we wish to estimate depth values, along with the transformation matrices between different images. 

Zhou et al. proposed two neural networks for estimating these values separately[[3]](#3). Namely, the first autoencoder takes an RGB image as input, and predicts the depth map by regression. The second network takes two images, and predicts transformation parameters between these images (rotation and translation, 6 Degrees-Of-Freedom). Their network is:

<img src="/images/paper_review1/zhou.png" height="250">

> "How can we provide supervision to predict depth and transformation parameters correctly?"

Getting back to the concepts I have introduced, photo-consistency assumes that the objects (3D points) in environment should have same color values on different images. To check that, we can warp the first image to the second one by the estimated parameters, and check if it has same color value with the target image (the next image which is the ground truth). See how it works:

<img src="/images/paper_review1/zhou_vs.png" height="250">

<img src="/images/paper_review1/Lvs.png" height="50">

Middle frame is warped by estimating depth map and transformation between the previous and next frames(t-1 and t+1). Then these frames are reconstructed through estimated T and D values. View synthesis loss compares the reconstructed frame and the ground truth frame. 
Here, T describes the motion of the camera and assumes that no other object moves in the space. There should not be major occlusions/disocclusions. Hence, dynamicity is minimum or none in the scene. Also we assume that the surfaces should be Lambertian. 

<p align="left" style="font-size:110%; font-weight:bold;">Contributions of the Paper</p>

The main contributions of our paper are:
1. To improve Zhou et al's model for dynamic environments because real driving setting is highly dynamic with all the cars and objects moving in the traffic.
2. To overcome domain adaptation problems particularly apparent in monocular depth estimation. 

<img src="/images/paper_review1/network.png" height="250">


<p align="left" style="font-size:110%; font-weight:bold;">Baseline Model</p>

Author’s use the same neural network from Zhou’s, with these loss functions:
1.*L_reconstruction*: Error between the ground truth frame and the synthesized view.

2.*L_SSIM*: It was introduced by Wang et al. as another metric to compare images[[11]](#11). Instead of errors between pixels, it compares the luminance, contrast and brightness of images through the following formula:

<img src="/images/paper_review1/SSIM.png" height="70">

3.*L_smoothness*: It is for further depth regularization. It is similar to total variation, but encourages depth map to have smooth edges on matching edges from RGB image. 

<img src="/images/paper_review1/Lsm.png" height="70">

Here, inputs are three consecutive frames 1-2-3 and the warpings are done between 1-2 and 2-3. They choose the one with the minimum error as suggested by Godard [[6]](#6) to prevent any major occlusions that might occur and create huge unwanted errors:

<img src="/images/paper_review1/Lrec.png" height="40">


<p align="left" style="font-size:110%; font-weight:bold;">Motion Model</p>

Authors propose to estimate motion of each object in the scene separately, and combine them to create the final view. To do that, they precompute binary masks of objects through Mask R-CNN and apply to the scenes (shown as elementwise production in formulas). Then they:

1.Remove all objects from scene to have a static background to estimate ego-motion. We are left with background scenes without any moving objects. S_i are the frames and O_i are the binary masks of objects. They remove the objects from all three consecutive frames to really have the pixels that cannot possibly have any moving object. V is the binary mask to represent static background pixels. 
<img src="/images/paper_review1/layout.png" height="35">
2.Apply estimated ego-motion to all frames to remove the effect of ego-motion. V is the binary mask of the background image, dot product refers to applying binary mask to an image. Hence, three consecutive images are multiplied with the background masks. It means that the remaining images only have background pixels, where the object pixels are now 0 or white. E are the predicted egomotion parameters

<img src="/images/paper_review1/egomotion.png" height="30"> 

3.Then for each object, estimate it’s motion separately. To do that, we first select an object from the scenes, apply it's own mask and left with three scenes where only the object's pixels are left and the remaining pixels are black. Then, we apply the camera's ego-motion (estimated on the previous step) to first and third images. We do that to eliminate the motion effect of camera, and to correctly perceive the motion of that object.  Then, we can use these there frames to estimate the motions M_i of each object:

<img src="/images/paper_review1/objectmotion.png" height="80">
Again, we predict the motion parameters for each separate object seen in the frames. 

4.Reconstruct the new frame for each object and background by applying the masks. Finally, we can first synthesize the background middle frame by the estimated ego-motion. Then reconstruct the object pixels for that same middle frame by the estimated object motions. After reconstructing these frames, we apply the relevant mask values to have unique values at each pixel location. 

<img src="/images/paper_review1/combine.png" height="90">

By the mask multiplication, it is guaranteed that no pixel from reconstructions are intersecting.   

<p align="left" style="font-size:110%; font-weight:bold;">Object Size Constraint</p>

A common issue in these models is the *infinite-depth degeneracy*. When an object is moving at the same speed with the camera, the network estimates infinite depth values to the object. Because normally, each object in the scene should have some motion due to camera's motion. If the object motion is not observable, then it must be very far away that it is not possible to perceive it's motion.

Another issue in monocular depth prediction is sometimes to estimate ridiculous depth values because the average shapes/heights of the objects are unknown. An object might be regarded as too close to the camera with a small motion, or far away from camera with a small motion.

To prevent such problems, authors propose using some weak priors to lead meaningful depths. If an average height of the object is known, depth can be calculated directly:

<img src="/images/paper_review1/weakp.png" height="200">

<img src="/images/paper_review1/weakd.png" height="30">

Where h is the pixelwise height of the object and f focal length of camera. Ideally, the calculated depth should be same with networks’ depth estimation for that object. Author’s model use differentiable weak priors for each different object category. 

<img src="/images/paper_review1/Lsc.png" height="80">

They also divide with mean depth values to prevent shrinking priors and numerical issues.


<p align="left" style="font-size:110%; font-weight:bold;">Online Refinement for Domain Transfer</p>

Finally, when a model trained on KITTI dataset is tested on a different driving dataset, this new dataset is regarded as a different domain because there are some inconsistencies:
1. Global scales between them do not match, they are taken with different cameras, different angles/heights.
2. Temporal shifts between frames may mismatch due to fps settings. 

To alleviate this, authors propose using the test data to train the model on the fly. Hence, each test data is iterated on the model for training and model adapts itself for the new dataset. They propose using each item for 20 times as ideal. 


# Experiments

Authors run their experiments on these datasets:
1. KITTI (~40K images total, LIDAR depth sensor)
2. Cityscapes (~4K images especially high dynamicity)
3. Fetch Indoor Navigation (~1.5K images, collected from a robot indoor environment, used for online refinement )

And they qualitatively evaluate the results in:

1.Depth metrics:
<img src="/images/paper_review1/depth_metrics.png" height="80">

Threshold percentages are better when higher, others(errors) are lower better.

2.Odometry metric: 
Absolute Trajectory Error is the absolute difference between the points of estimated trajectory and the ground truth. Lower is better. The results show the mean and standard variation.

Their depth and odometry results for KITTI establish SOTA according to these tables from paper:

<img src="/images/paper_review1/kittidepth.png" height="220">

<img src="/images/paper_review1/kittiodo.png" height="250">

As can be seen, the motion based model is slightly worse than the previous SOTA results. However their improvement is clear with the refinement model. Personally, I believe that refinement is an independent step from the model and they should apply it to the other models for unbiased comparison. 

The depth results especially show improvements on infinite-depth degeneracy problem as can be seen in the images below. The objects in red circle are moving at same speed with the camera and the baseline model predicts infinite depth values for them. Yet, Casser's method eliminates this degeneracy.

<img src="/images/paper_review1/infin.png" height="300">

Finally, they applied online refinement on a Cityspaces trained model with Indoor Fetch dataset. Their motion model show some improvement on the baseline, but the refinement model makes it much better qualitatively. Also depth maps become better(sharper and clearer) as seen: 

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

Writers provide their model network in Tensorflow code [struct2depth](https://github.com/tensorflow/models/tree/master/research/struct2depth). I have run their code both on a KITTI sequence and a random driving video from Youtube. To prepare the object instance labels, I used matterport's [Mask R-CNN implementation](https://github.com/matterport/Mask_RCNN). As preparing the data was a little tedious, I provide some extra code and the jupyter lab files that I have used [here](https://github.com/evinpinar/struct2depth_dataprep).

<img src="/images/paper_review1/melbourne.gif">

<img src="/images/paper_review1/kitti_output.gif">


To sum up, I think this paper is a good attempt to alleviate the unrealistic static scene assumption and it supports flexible environments with self-supervised learning for autonomous driving. I believe there are many questions and problems in this area that can be tackled. 

<sub><sup>Here is a link for [presentation slides](https://drive.google.com/file/d/14oNrfLpiwHwWuJC5wNM-7XjZzmFoCYqM/view?usp=sharing) of this blog post.<sub><sup>


<p align="left" style="font-size:90%; font-weight:bold;">References</p>


<a name="1"> [1] </a> V. Casser, S. Pirk, R. Mahjourian, and A. Angelova, “Depth Prediction without the Sensors: Leveraging Structure for Unsupervised Learning from Monocular Videos,” in Thirty-Third AAAI Conference on Artificial Intelligence (AAAI-19), 2019.  
<a name="2"> [2] </a> A. Geiger, P. Lenz, C. Stiller, and R. Urtasun, "Vision meets Robotics: The KITTI Dataset", in International Journal of Robotics Research, 2013.  
<a name="3"> [3] </a> T. Zhou, M. Brown, N. Snavely, and D. G. Lowe, “Unsupervised Learning of Depth and Ego-Motion from Video,” in CVPR, 2017.  
<a name="4"> [4] </a> D. Eigen, C. Puhrsch, and R. Fergus, “Depth Map Prediction from a Single Image using a Multi-Scale Deep Network,” in Advances in Neural Information Processing Systems 27, Z. Ghahramani, M. Welling, C. Cortes, N. D. Lawrence, and K. Q. Weinberger, Eds. Curran Associates, Inc., 2014, pp. 2366–2374.  
<a name="5"> [5] </a> I. Laina, C. Rupprecht, V. Belagiannis, F. Tombari, and N. Navab, “Deeper depth prediction with fully convolutional residual networks,” in 3D Vision (3DV), 2016 Fourth International Conference on, 2016, pp. 239–248.  
<a name="6"> [6] </a> C. Godard, O. Mac Aodha, and G. J. Brostow, “Unsupervised Monocular Depth Estimation with Left-Right Consistency,” in CVPR, 2017.  
<a name="7"> [7] </a> B. Ummenhofer et al., “DeMoN: Depth and Motion Network for Learning Monocular Stereo,” in IEEE Conference on Computer Vision and Pattern Recognition (CVPR), 2017.  
<a name="8"> [8] </a> H. Zhan, R. Garg, C. Saroj Weerasekera, K. Li, H. Agarwal, and I. Reid, “Unsupervised Learning of Monocular Depth Estimation and Visual Odometry With Deep Feature Reconstruction,” in The IEEE Conference on Computer Vision and Pattern Recognition (CVPR), 2018.  
<a name="9"> [9] </a> C. Luo et al., “Every Pixel Counts ++: Joint Learning of Geometry and Motion with 3D Holistic Understanding,” IEEE Transactions on Pattern Analysis and Machine Intelligence, pp. 1–1, 2019.  
<a name="10"> [10] </a> Z. Yin and J. Shi, “GeoNet: Unsupervised Learning of Dense Depth, Optical Flow and Camera Pose,” in 2018 IEEE/CVF Conference on Computer Vision and Pattern Recognition, 2018, pp. 1983–1992.  
<a name="11"> [11] </a> Zhou Wang, A. C. Bovik, H. R. Sheikh, and E. P. Simoncelli, “Image quality assessment: from error visibility to structural similarity,” IEEE Transactions on Image Processing, vol. 13, no. 4, pp. 600–612, Apr. 2004.  
<a name="12"> [12] </a> R. Garg, B. G. V. Kumar, G. Carneiro, and I. Reid, “Unsupervised CNN for single view depth estimation: Geometry to the rescue,” in European Conference on Computer Vision, 2016, pp. 740–756.  


