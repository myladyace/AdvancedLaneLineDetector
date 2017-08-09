## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Advanced Lane Finding Project**

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

[image1]: ./output_images/undistortion_chessboard_example/original_vs_undistorted.png "Undistorted"
[image2]: ./output_images/undistortion_test_images/original_vs_undistorted_test1.png "Road Transformed"
[image3]: ./output_images/binary/binary.png "Binary Example"
[image4]: ./output_images/warped_images/original_vs_warped.png "Warp Example"
[image5]: ./output_images/polyfit/polyfit.png "Fit Visual"
[image6]: ./output_images/radius/radius_center_test1.jpg "Output"
[image7]: ./output_images/binary/sobel_hls.png "Sobel and color transform"
[image8]: ./output_images/binary_warp/binary_warp.png "Binary Warp"
[video1]: ./processed_project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the third code cell under headline "Camera Calibration" of the IPython notebook located in "./Project.ipynb".  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image('./camera_cal/calibration1.jpg') using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

Actually I just used the cv2.undistort() with the mtx and dist found above. The code is under headline "Test the undistortion in a test image" of the Project.ipynb. 

You can easily find the difference between these two images, especially on the edges of the image.

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image.
There are 3 cells under the headline "Using Color Transform and Gradient to create a binary image" in the Project.ipynb.
First cell contains all 6 methods I may use, 3 sobels and 3 channels under HLS channels.
The second cell test all 6 method individually on test1.jpg.
The result is below:

![alt test][image7]

The third cell includes the method I think that provides good output. I found using the combination of sobelx/direction of gradient/s_channel/h_channel can give a good output. 
Here's an example of my output for this step.

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warp()`, which appears in the first cell under headline 'Perspective Transform'. 
The `warp()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points. 
I chose the source and destination points by applying several combination of them on the test image and choose the most accurate one.
The source and destination points are below:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 578,460      | 230, 0        | 
| 190, 720      | 230, 720      |
| 1130, 720     | 1050, 720      |
| 705, 460      | 1050, 0        |

While applying it on the './test_images/straight_lines1.jpg', the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I used the moving window method and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

The code can be found under headline "Identify pixels using moving windows" of the Project.ipynb.

I also provide a method under the healine "Identify pixels using previous frame". All the codes are from udacity lecture.

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this under the headline "Measuring curvature" in the function measure_curvature().

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in the code cell under headlines "Drawing the image" in the function `draw_image()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./processed_project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

My pipeline seems good for this project, but maybe fail in more extreme case like very bright or dark environment. I think introduces some more color channels may be good to make this model more robust.  
