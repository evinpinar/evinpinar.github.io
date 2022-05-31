---
layout: post
title: Tab cleanup day - A stack of 3D geometry/ML papers 
categories: [research,CVPR]
comments: true
---

After a couple of deadlines, I finally have a day to clean up the tabs in my phone (not laptop, which I do often!). Typical process: transfer the tabs from phone to laptop, go through the abstracts, see if any of them is useful for my current/future research. Now I've decided to share the papers collected in my stack with brief summaries to convert this mundane/passive task to something more productive. This is a random stack of 3D geometry and machine learning. I reorder them in terms of topics - so not showing the real order from my tabs. 

<p align="left" style="font-size:110%; font-weight:bold;">1. <a href="https://phog.github.io/snerg/">Baking Neural Radiance Fields for Real-Time View Synthesis</a> (ICCV 2021)</p>

<img src="https://phog.github.io/snerg/img/breakdown.png" height="75">

In an attempt to make NERFs real-time renderable, this paper uses the idea of "baking". Seemingly, "baking" is a common term in graphics, and it means "consolidating a system of data into a simplified form". It is basically organizing the information in a way to make retrieval faster. It can be texture, lighting or action. In the paper, it refers to storing the neural fields in a more efficient way using the sparse grids. NERF typically consists of MLPs that take input 3D coordinate to give an color and density value. When rendering, one should sample many points to approximate the continuous space, yet it is time consuming. This paper enables precomputing and storing the values in a 3D voxel grid like a lookup table. Training is done on a typical but slightly reformulated version of NERF, and test time includes precalculation of the sparse grid for fast lookup. Finally, it becomes a kind of neural implicit - explicit representation.     

<p align="left" style="font-size:110%; font-weight:bold;">2. <a href="https://arxiv.org/pdf/2102.07064.pdf">NeRF−−: Neural Radiance Fields Without Known Camera Parameters</a></p>

<video loop="" class="center_video" controls="" autoplay="" muted="">
    <source src="assets/comparisons/flower_colmap_learning_traj.mp4" type="video/mp4">
  Your browser does not support the video tag. flower-COLMAP-ours-traj
  </video>

As mentioned above, NERF requires camera poses of images, which can be difficult to acquire in real world. This paper instead enables training without known poses. They introduce camera parameters (intrinsic f, extrinsic R,t) which are optimized along with NERF MLP, supervised by images only. I think initialization would be an issue here, and paper states cameras are initiated to be at origin looking at z, with focal length set to image width. And I guess the rest is deep learning magic (and engineering?). At the time of the writing, it looks like this paper is not published, but all the code, data and details are available.

### [NeRF-Pose: A First-Reconstruct-Then-Regress Approach for Weakly-supervised 6D Object Pose Estimation](https://arxiv.org/pdf/2203.04802v1.pdf)

6D object pose estimation often refers to task of detecting an object's 3D location and 3D orientation from a single image. NNs are usually trained by annotated sets of 3D object models and their poses. Yet, what happens when we don't have enough data? Also, CAD model may not be fitting perfectly on the image if it's a real one. This paper instead relies on 2D segmentation (instead of 3D model) and camera pose. It first builds an object-nerf from multiple views of an image, supervised by rendering both the image and mask. Then trains a single view pose estimation network supervised by object-nerf rendered mask and NOCS (Normalized Object Coordinate Space, a 3D map showing pixel coordinate wrt canonical object coordinates). Applying the mask on NOCS and Pnp+RANSAC, voila, the 6D object pose!  

### [ReLU Fields: The Little Non-linearity That Could (SIGGRAPH 2022)](https://arxiv.org/pdf/2205.10824.pdf)

This paper trains a NERF without neural MLP layers! They replace the MLP simply with a 3D RELU voxel grid (a 3D voxel of features whose vertice values are evaluated by a ReLU). It sounds very simplistic right? Then a question arises, what is a neural network, why is it strong, due to neural layers, or due to non-linear operations? Maybe structuring the input in a nice way (MLP, conv, transformers) helps the optimization, but in the end job is done by non-linearities (relu, sin, tanh)...  Mentioning that, it would be nice to see a SIRENized version of this work. 


### [Multi-View Consistent Generative Adversarial Networks for 3D-aware Image Synthesis (CVPR2022)](https://arxiv.org/pdf/2204.06307.pdf)

Continuing with the neural implicit fields, there is a line of research looking at stylistically aware 3D consistent shape generation (PiGAN, StyleSDF, StyleNerf), which enable extracting a mesh of a Style-GAN face. Problems are, PiGAN does not explicitly train with 3D constraints, ending up low-quality 3D mesh. Whereas StyleSDF and StyleNerf train only with an image-camera pose inputs, which are stronger than PiGAN. This work, on the other hand, trains the field with explicitly warping the images with known poses to introduce additional multiview photometric consistency. Noting that StyleSDF is also a CVPR paper, and I believe StyleNerf is not yet published, it will be interesting to see their comparison in the future. 


### [AutoSDF: Shape Priors for 3D Completion, Reconstruction and Generation (CVPR 2022)](https://yccyenchicheng.github.io/AutoSDF/)

The previous paper focused on human and cat faces, now we are challenged with real world objects, and the tasks can differ from single-view reconstruction, to shape completion and language-guided generation. This work proposes an object shape representation which is decoupled from the task, and can be used with a minimal conditioning supervision. The idea is first decomposing the object into small patches and learning a latent prior for each patch through VQ-VAE(Vector quantized variational autoencoder). To enable conditional generation, they use a Transformer based model which takes the latent priors and autoregressively predicts the joint shape distribution. When this model is trained for full 3D generation, one can give input the partial scan and complete it through the known priors. Otherwise, for language grounding, autoregressive model is biased by the word tokens to enable conditioned generation, so one can generate chair with round legs or a couch on wheels. I wonder how would zero-shot compositionality work in this scenario.


### [Sharp Edge Recovery Challenge (CVPR 2022)](https://www.artec3d.com/sharp2022#challenge-2)

It's a CVPR2022 challenge with generous awards! There are two tasks: (1) reconstructing a full 3d textured mesh of a body or object from a partial scan (2) recognizing a 3d object scan and reconstructing in a parametric form with lines and edges (CAD).   


### [Domain Disentangled Generative Adversarial Network for Zero-Shot Sketch-Based 3D Shape Retrieval](https://arxiv.org/pdf/2202.11948.pdf)

The title already explaining the task, they train a model which takes a sketch and its 3D shape, which first disentangles domain specific and invariant features, where the invariant ones are aimed to embed in word space. Then at train time, they combine different domain specific features along with semantics, optimized with a reconstruction and a discrimination(real/fake) loss. Since generator is conditioned on domain invariant features along with semantics, when a novel shape sketch is given with its name, the model can generate a 3D shape. 

### [Lifting 2D Object Locations to 3D by Discounting LiDAR Outliers across Objects and Views](https://arxiv.org/pdf/2109.07945.pdf)
This is a cool geometry paper, where the problem is getting 3D bounding boxes from image and LIDAR point cloud data. The problem with LIDAR is, there are many occlusions, and most methods aim to regress bounding box on the occluded car data. This paper solves it by assuming a 3D car shape prior, which is shared between all cars, and tries to fit the segmented car point cloud into the model. Segmentation is retrieved from Mask R-CNN, and getting the corresponding points by known camera parameters, learning an outlier detector to filter out background points (due to imperfect mask). Then the remaining points are fit to a mesh, and pose is regressed by the mesh bounding box coordinates. This is a cool idea, and one step further would be enabling learning different types of car models. 

### [Deep Matching Prior: Test-Time Optimization for Dense Correspondence (ICCV 2021)](https://openaccess.thecvf.com/content/ICCV2021/papers/Hong_Deep_Matching_Prior_Test-Time_Optimization_for_Dense_Correspondence_ICCV_2021_paper.pdf)
Dense correspondense aims to match the pixels within two given images (optical flow). There are two class of methods (1) optimization - such as variational methods, aims to maximize similarity in between matched results (2) deep learning - needs large data to train, annotation. This paper combines the two, where a network is optimized for a target-source image without annotation. The idea is based on using Imagenet-resnet features, and again maximizing the similarity (contrastive loss). There are some tricks like, searching on a neighborhood, pyramid formulation. They show applying augmentation (homography, affine, thin plate spline (TPS)) to the pair, as well as pre-training, improves performance. It is such a simple yet strong idea, and a well-written paper. I particularly enjoyed reading it!  


### [LiT : Zero-Shot Transfer with Locked-image text Tuning (CVPR 2022)](https://arxiv.org/pdf/2111.07991.pdf)

Continuing with contrastive learning, this paper takes a detailed look in data transfer capabilities of image-text models. Zero-shot transfer as in GPT-3 or CLIP, refers to training a model in a task agnostic way with a large dataset, and adapting it to a particular task ("Language models are few-shot learners"). For image-text models, this refers to having a text-image pair contrastively trained model, and testing on a novel class from a novel image dataset. This paper provides an in-depth analysis comparing (1) pre-training of text-image towers in contrastive setup, (2) using already trained towers (Imagenet classification) and fine-tuning in contrastive. Finally, they show that locked image model allows more generalizability across different datasets. Is is a Google paper, and it is nice to see resources getting used for such clear analysis papers, which are useful for the whole community.


### [Socratic Models: Composing Zero-Shot Multimodal Reasoning with Language](https://arxiv.org/pdf/2204.00598.pdf)

The image-text models mentioned above use paired images on internet, yet the text only large-models often rely on additional external textual data, such as spreadsheets, code, novels etc. This paper looks on how to get the best of these models and combine them in zero-shot setting, asking the question "to what extend we can use lang or lang-text model on different multimodal tasks such as robot planning or audio". By using the already existing models, they introduce a new method, Socratic Model, to combine and connect modalities, which is based on function composition (in broad terms). For example, the sota image captioning functions VLM (visual language model) trained on an image, creates a capture. Socratic Model uses a LM to enhance the caption, which is then fed back to VLM to get the plausibility of the answer. In this way, their results show that LM's strength can be in use. Some other tasks they demonstrate: video-to-text retrieval through LM, VLM, ALM(+audio), egocentrict perception (+user modality), robot perception and planning (adding policy methods). The idea reminds me of ensemble models a bit, and multiview consistency checking, where the idea is confirming if different hypothesis match. Overall, it serves for general purpose AI understanding. 


### [Embodied AI Workshop in CVPR 2022](https://embodied-ai.org/)

Continuing the Habitat workshops, this one aims to couple the progress in computer vision, grounded understanding with NLP, audio signals, navigation, interaction and reasoning within the environment. It includes around 13 different tasks covering all these problems as subtasks (interactive navigation, semantic SLAM, vision-dialog interaction etc.) It is particularly interesting, as it aims to brings us a step closer to R2D2 :P by uniting all the research directions. There is a big family of organizers from all areas.

### [How does Astro localize itself in an ever-changing home?](https://www.amazon.science/blog/how-does-astro-localize-itself-in-an-ever-changing-home)

It is a new indoor house robot from Amazon, in production stage, comes with a screen, two tires and a small carriage area. It can navigate within the house with visual-SLAM, communicate to people and handle tasks, like security monitoring, delivering calls/reminders, playing shows. Another step closer to R2D2, or? :) Things are getting very exciting nowadays, and seeing the research getting into real-life and use is indeed motivating.


### [Holistic 3D Scene Understanding from a Single Image with Implicit Representation](https://arxiv.org/pdf/2103.06422.pdf)

All you need is a single image paper! They estimate room layout, detect the object's 3D locations, reconstruct their shapes, and refine the overall structure through building a scene graph. They compare the object reconstructions with AtlasNet and MGN, and the holistic scene reconstruction with Total3D (CVPR2020). They have good geometric results but perhaps could be improved by semantics as in [here](https://openaccess.thecvf.com/content/ICCV2021/papers/Dhamo_Graph-to-3D_End-to-End_Generation_and_Manipulation_of_3D_Scenes_Using_Scene_ICCV_2021_paper.pdf). 


### [Graph Theory and Linear Algebra](https://www.math.utah.edu/~gustafso/s2017/2270/projects-2017/dylanJohnson/Dylan%20Johnson%20Graph%20Theory%20and%20Linear%20Algebra.pdf)

A recap on graph representation with matrices, notes on their eigenvalues and ranks, and interesting properties of complete graphs and acylic graphs (trees) through linear algebra.


### [Sign and Basis Invariant Networks for Spectral Graph Representation Learning (ICLR 2022 Workshop)](https://arxiv.org/abs/2202.13013)

Graphs can be processed with Transformers, by using the Laplacian eigenvectors as positional embeddings. However, the sign of the eigenvectors can vary (1/-1) and the positional embedding is unaware of this property, i.e., not invariant to sign. Furthermore, there might be eigenvectors with >1 multiplicity (occuring multiple times) which does not effect the eigenspace, hence, should not bia the Transformer. Practically, one can apply augmentation covering these different scenarios, but cannot guarantee strong generalization. This paper aims for sign and basis invariant functions, and comes with an extensive supplementary material to explain all the concepts behind. This could have interesting results on manifold learning, which was demonstrated as an example on page 26-27. 


### [Self-Supervised Transformers for Unsupervised Object Discovery using Normalized Cut (CVPR 2022)](https://www.m-psi.fr/Papers/TokenCut2022/)

DINO last year proposed a self-supervised ViT learning method, based on self-distillation (teacher student trained on different views of same image), where the learned Transformer gives meaningful attention masks, could be directly used for classification (by knn), and promising use-cases for segmentation. And TokenCut (this cvpr paper) realized self-supervised segmentation, by using the DINO attention maps and a graph-cut algorithm. Conceptually, the idea is clustering similar DINO features to separate background and foreground. Specifically, build a graph where edges represent similarity of features, and learn a bipartition of this graph by NCut.


 









