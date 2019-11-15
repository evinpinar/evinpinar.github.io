---
layout: post
title: Paper Review on Monocular Depth Prediction
categories: [AI, tech]
comments: true
---


In this blog post, I will review the paper [Depth Prediction Without the Sensors: Leveraging Structure for Unsupervised Learning from Monocular Videos](https://arxiv.org/abs/1811.06152) published in AAAI 2019. After briefly introducing the topic and the relevant concepts, I will explain the method in my own words. Then we will discuss the results and the future works. 

## Introduction

Depth prediction is a fundamental task for us to perceive the 3D environment around us. Through depth inference, we can grab the objects, move around and perform our daily tasks. To be able to create autonomous objects that can perform similar tasks to people, we should model this 3D perception. Autonomous cars should detect the other cars and pedestrians, and plan their trajectory in 3D environment. Likewise, robots need to move in their environment, grab things, carry to some distance and replace them. 

Currently, depth estimation relies on sensors such as LIDAR and radar or RGBD cameras. However, there are two problems in this setting. First, these sensors return sparse inputs, especially perform poorly on edges, far points and uncommon surfaces. Second, they are extremely expensive to allow mass production. For these reasons, it is highly desirable to be able to estimate depth without external sensors, and only with an ordinary RGB camera. 

From another point of view, a question that commonly arise is “Can people perform depth inference perfectly with only one eye?”. Some believe that our depth prediction relies on perfectly calibrated two eyes, yet some others think that the second eye exists only for backup and does not effect our depth understanding.

Following this question, the research in depth estimation from RGB images is divided into two: stereo setting and the monocular setting. By stereo, I mean either images from stereo cameras, or the consecutive frames of videos taken with a single camera. By monocular, a single image without any previous or next similar frame. Also, the stereo setting inherently creates further opportunities in unsupervised (self-supervised) learning. Practically, self-supervision is very useful since we do not need any ground truth annotations!  


## Concepts

There are some ideas and keywords that occur in the literature that we should be aware of first. These concepts would be familiar to you if you have taken a Multi-View Geometry course. Otherwise, I will try to explain in very simple and clear way. 

#### Rigid Body Motion

The motion in 3D space is formalised by Euclid, hence the "Euclidean coordinates" usually refers to 3D coordinates that concern us in real life. Here, we can define the motions of rigid objects as rotation and translation.

<img src="/images/paper_review1/se3.png" height="75">

We can apply this matrix to any 3D point to get the new coordinates of point after a motion.

<img src="/images/paper_review1/xvec.png" height="20">

<img src="/images/paper_review1/rbd1.png" height="30">

#### Perspective Projection

The study of projecting the 3D points to 2D images taken with a camera is "perspective projection". For such a pin-hole thin lense camera model with a focal length f:

<img src="/images/paper_review1/focal.png" height="150">

We can retrieve the image x and y coordinates by:

<img src="/images/paper_review1/focal2.png" height="100">

In vector form, it is same with:

<img src="/images/paper_review1/focal3.png" height="100">

And finally, the standardized notation in homogeneous coordinates is: 

<img src="/images/paper_review1/focal.png" >


#### Structure vs Motion

Here, "structure" refers to estimating the real world coordinates of points, and specifically the depth values. It is about recovering the geometry. Wheras "motion" refers to recovering rigid body motion (transformation) of these points. To be have a full understanding of 3D environment, we should estimate both. If we know one, the estimation of the other becomes easier.


#### Odometry

It is the study of estimating motion of an object. Ego-motion refers to estimating the camera's 3D motion.

#### Photo-consistency

A 3D voxel should have same colors on different images taken from different angles. With this assumption, we can take two images of the same scene, and find the transformation between images. 


#### Lambertian surface

It is the ideal matte surface where the brightness is same from different angles. The shiny, metal or glass surfaces in the scene are non-Lambertian.

#### Image warping

It literally means moving the pixels of images. For example, we can multiply the x coordinate pixels with two, and the image will look twice bigger horizontally.


