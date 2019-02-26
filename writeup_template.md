# Advanced Lane Detection using OpenCV and Python

## Overview

This project walks you through some of the advanced concepts in finding lane lines using OpenCV library functions. This project includes passing an image through series of pipeline steps to extract lane information. 

## Steps followed in this project are as follows

* **Camera Calibration:** Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* **Distortion Correction:** Apply a distortion correction to raw images.
* **Extract Lane information:** Use color transforms, gradients, etc., to create a thresholded binary image.
* **Perspective Transform:** Apply a perspective transform to rectify binary image ("birds-eye view").
* **Lane detection:** Detect lane pixels and fit to find the lane boundary.
* **Compute Curvature of Lanes:** Determine the curvature of the lane and vehicle position with respect to center.
* **Annotation of Detected Lanes:** Warp the detected lane boundaries back onto the original image.
* **Output Final Image:** Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.


## Camera Calibration

First step is to eliminate any unwanted distortion in the image introduced by the camera. This is very important so as to compute the curvature of the lanes correctly.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

Here's is the output of distortion correction

<p align="center">
<img src='camera_cal/calibration1.jpg' alt="Original Image" width='45%'>  <img src='output_images/undist_calibration1.jpg' alt="Undistorted Image" width='45%'>
</p>


## Pipeline Stages

### 1. Distortion Correction

Use the camera matrix and and distortion coefficients computed in above step to undistort images. Following is the one example of distortion corrected image.

<p align="center">
<img src='test_images/test6.jpg' width='45%'>  <img src='output_images/undist_test6.jpg' width='45%'>
</p>


### 2. Gradients and Color Threshold

#### Gradient Threshold

The undistorted image is then passed through function `apply_grad_thresholds()` to apply gradients in x and y direction and also compute magnitude gradients separately and then combine these images.

Here is the output image after applying gradients
<p align="center">
<img src='test_images/test6.jpg' width='45%'>  <img src='output_images/gradient_test6.jpg' width='45%'>
</p>

#### Color Threshold

Universally the lane lines on the road are marked in **_White_** and **_Yellow_**, and after trying with different color spaces found out that the colors Yellow and White are more prominent in **_HSL(Hue Saturation and Light)_** color space.

Here are the filter values which worked best for me
##### Filtering White Lines
From the above model you can easily observe that white colors are present at the top of the cylinder, i.e. higher Lightness value regardless of Hue and Saturation.
    
    Hue:        no-filter
    Lightness:  195 to 255
    Saturation: no-filter

##### Filtering Yellow Lines

    Hue:        18 to 32
    Lightness:  120 to 255
    Saturation: 90 to 255

Here is the output of `apply_color_thresholds`
<p align="center">
<img src='test_images/test6.jpg' width='45%'>  <img src='output_images/hsl_test6.jpg' width='45%'>
</p>

##### Combining the two threshold images we get

<p align="center">
    <img src='output_images/threshold_test6.jpg' width='45%'>
</p>


### 3. Perspective Transform

The function `warp_image()` takes binary threshold image and creates the warped image.

The `warp_image()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
    src = np.float32(
            [[586, 454], 
            [698, 454], 
            [272, 674], 
            [1054, 674]])
    
    dst = np.float32(
            [[225, 0], 
            [900, 0], 
            [225, 702], 
            [900, 702]])
```

After applying perspective transform on test images we get the following output

<p align="center">
<img src='test_images/test6.jpg' width='45%'>  <img src='output_images/warped_test6.jpg' width='45%'>
</p>

<p align="center">
<img src='output_images/threshold_test6.jpg' width='45%'>  <img src='output_images/warped_binary_test6.jpg' width='45%'>
</p>

### 4. Identify Lane Pixels

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:


### 5. Compute Radii of Curvature

I did this in lines # through # in my code in `my_other_file.py`

### 6. Annotation

I implemented this step in lines # through # in my code in `yet_another_file.py` in the function `map_lane()`.  Here is an example of my result on a test image:


---

## Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  
