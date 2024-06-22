# SFND 3D Object Tracking

Welcome to the final project of the camera course. By completing all the lessons, you now have a solid understanding of keypoint detectors, descriptors, and methods to match them between successive images. Also, you know how to detect objects in an image using the YOLO deep-learning framework. And finally, you know how to associate regions in a camera image with Lidar points in 3D space. Let's take a look at our program schematic to see what we already have accomplished and what's still missing.

<img src="images/course_code_structure.png" width="779" height="414" />

In this final project, you will implement the missing parts in the schematic. To do this, you will complete four major tasks: 
1. First, you will develop a way to match 3D objects over time by using keypoint correspondences. 
2. Second, you will compute the TTC based on Lidar measurements. 
3. You will then proceed to do the same using the camera, which requires to first associate keypoint matches to regions of interest and then to compute the TTC based on those matches. 
4. And lastly, you will conduct various tests with the framework. Your goal is to identify the most suitable detector/descriptor combination for TTC estimation and also to search for problems that can lead to faulty measurements by the camera or Lidar sensor. In the last course of this Nanodegree, you will learn about the Kalman filter, which is a great way to combine the two independent TTC measurements into an improved version which is much more reliable than a single sensor alone can be. But before we think about such things, let us focus on your final project in the camera course. 

## Dependencies for Running Locally
* cmake >= 2.8
  * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1 (Linux, Mac), 3.81 (Windows)
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* Git LFS
  * Weight files are handled using [LFS](https://git-lfs.github.com/)
  * Install Git LFS before cloning this Repo.
* OpenCV >= 4.1
  * This must be compiled from source using the `-D OPENCV_ENABLE_NONFREE=ON` cmake flag for testing the SIFT and SURF detectors.
  * The OpenCV 4.1.0 source code can be found [here](https://github.com/opencv/opencv/tree/4.1.0)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools](https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory in the top level project directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./3D_object_tracking`.

FP.1 Matching 3D Objects 
This task was completed by tracking pairs of bounding boxes IDs and then counting the number of same keypoints per box in consecutive images to see what the best pair of bounding boxes to associate would be

FP.2 Compute Lidar-based TTC
This task was rather straight forward as once we had associated the bounding boxes from one image to the next, it was determined to take the median x coordinate of all Lidar points in one image and the following. The sorting to get the median was done using a helper function: HelperSortLidarPoints. Once x coordinates were median x coordinates were taken, we used the distance formula time = distance / speed, where distance is current distance and speed is the velocity found from the difference of x cooridnates over the framerate. 

FP.3 Associate Keypoints with Bounding Boxes
This was done by looping over all bounding boxes and if a keypoint is found within a bounding box, it will be added to this struct. Keypoints in multiple will fallout.

FP.4 Compute Camera-based TTC
This was done by using the distance formula again but by getting the distance ratio from point to point in the desired bounding box of interest. The median distance ratio was then computed to determine the time to collision using tim = distance / speed where distanceRatio * rate = time.

FP.5 Performance Evaluation 1
Overall the Lidar performance was not bad and it ranged from ~7 to ~15 seconds across all images. I believe alot of the poor performance was reduced by using the median point rather than minimum. I did see that there was some images in which the results seemed lower and when I looked at the down view of the points there was few showing. This leads me to believe it was a bad scan for these images.

FP.6 Performance Evaluation 2
After running the camera TTC estimate across all detector/descriptor types (the results of this study can be found in Final Camera Project Performance Evaluation 2 Results.xlsx) I found some nominal results across the board except for a few. Harris as a detector with and descriptor resulted in a negative TTC. I believe this to be due to the large number of inaccurate points it generated, it most likely needed finer tuning for its edge detection inputs. ORB/AKAZE resulted in the lowest TTC at an average of 6.359 seconds. This most likely stems from the the key points moving quite a bit from one image to the next, making the velocity seem higher than it is. The largest average TTC was BRISK/BRISK with 27.6679 seconds, this most likely stemmed from the inverse of the keypoints being very close from one image to the next. All other detector/descriptor combos hovered around 12-14 seconds. 

Thanks!
