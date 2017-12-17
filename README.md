# Udacity Self-Driving Car Nanodegree Program 

# **4. Advanced Lane Finding Project** 

**Goals**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./examples/chessboard_points.png "Chessboard Points"
[image11]: ./examples/calibration1_undistort.png "Calibration 1 Undistorted"
[image2]: ./examples/straight_lines2_undistort.png "Straight Lines 2 Undistorted"
[image3]: ./examples/test4_color_gradient.png "Test 4 Color Gradient"
[image4]: ./examples/straight_lines2_birds_eye.png "Stright Line 2 Birds Eye"
[image5]: ./examples/straight_lines2_sliding_poly_fit.png "Straight Line2 Sliding Poly Fit"
[image6]: ./examples/straight_lines2_draw_lane.png "Straight Line 2 Draw Lane"

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained a list of undistorted images from the given test images. An example of undistorted chessboard image follows: 

![alt text][image11]

An example of undistorted test image follows:

![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image, in a `combined_binary()` function of the jupyter notebook.  Here's an example of my output for this step.  

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `birds_eye()` of the Jupyter notebook.  The `birds_eye()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 580, 460      | 200, 100      | 
| 200, 720      | 200, 720      |
| 706, 460      | 1040, 100     |
| 1140, 720     | 1040, 720     |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear nearly parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

After applying calibration, thresholding, and a perspective transform to a road image, I use histogram as a starting point for where to search for the lane lines in the base of the frame. I then use a sliding window, placed around the line centers, to find and follow the lines up to the top of the frame. This is implemented in the function `fit_with_histogram()`. 

![alt text][image5]

An optimization can be made to search in a margin around the line positions in the previous frame for lane lines instead of using more expensive histogram for every frame. This is implemented in the function `fit_with_last_fits()`.

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The left and right curvatures of the lane are calcualted from the derivatives of the left_fit and right_fit polynomials with a function `measure_curvature()`. I calculated the curvatures in image pixels first, then used another polyfill to calculate the curvatures in world space.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in the function `draw_lane()`.  Here is an example of my result on a test image:

![alt text][image6]

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a link to my video result on Youtube:

[![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/vYMJuPanJ6k/0.jpg)](https://www.youtube.com/watch?v=vYMJuPanJ6k)

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The solution works when lane lines are visually distinguishable from the backgrounds by the combinations of colors and gradients. This is a pure computer vision approach, where thresholds have to be carefully chosen to reach some acceptable results. The method is not robust for practical self-driving on complex road or weather conditions. I expect the future lessons will address the issue with more robust machine learning techniques.
