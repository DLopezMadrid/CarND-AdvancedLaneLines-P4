### **Advanced Lane Finding Project**

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

[cam_cal]: ./assets/cal1.png "Undistorted"
[warped]: ./assets/warped.png "Road Transformed"
[img_pipe]: ./assets/img_pipeline.png "Single img pipeline"
[diag]: ./assets/diag.png "diagnostics image"
[diag_key]: ./assets/diag_key.png "diagnostics image key"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

### Additional information
The code for the project is stored in the `P4-Advanced-Lane-Lines.ipynb` file.
### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

This part of the assignment was done following a very similar solution as the one presented during the lectures. First, I generate the `objpoints` and `imgpoints` necessary to perform the calibration. Then, by using the OpenCV library (specifically the `cv2.calibrateCamera()` function) I find the correction distortion by applying it to the chessboard images.

Once the distortion correction parameters are calculated, we can undistort images using the  `cv2.undistort()` function and obtain results like this:

![alt text][cam_cal]

### Pipeline (single images)

After applying the distortion correction to the images, I proceed to warp them into a top view. This is done with OpenCV's `getPerspectiveTransform` and `warpTransform` functions. The first one is used to obtain the transformation matrix by inputing the source points (`src`) and their destinated position on the new image (`dst`). This is an example of the obtained results:

![alt text][warped]

It is important to calibrate `src` and `dst` correctly to apply a transformation that remains true to what the road that is representing

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

The code for this section is on the `lane_detection` function

I used a combination of color and gradient thresholds to generate a binary image. Here's an example of my output for this step.

![alt text][img_pipe]

To obtain this results I used different techniques. For the white mask, I select a certain range of the **HLS** colorspace. For the yellow mask I use a similar technique on the **HSV** colorspace. Then, I apply a hough transform that helps to filter the unwanted parts of the mask and remark the bits that we want.

Parallel to this, I also use a Sobel mask by calculating the `x` and `y` sobel operators of the `L` and `S` channels of the **HLS** image.

All these masks are then combined into a single binary mask that is used to detect the lane lines afterwards.


#### 3. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

This section is done in the `process_video` function

In order to identify the lane pixels I follow two different approaches based on if its the first time that I am detecting the lanes or not.

For the first "blind search" (as written on the `init_lines` function), I start by dividing the image in two sections (left and right). Then, I do a histogram of the binary image (`bin_img`) created in the previous step and look for the highest peak on it, this will give me the position of the lane lines on the image. Once I have that initial position, I create a mask that covers the line plus a predefined window around it. This step was done on the bottom part of the image and then repeated 7 more times for a total of 8 mask sections that cover the lane line.

Once the line has already been found, I just update the mask based on the previously detected lane pixels +- a window. This way I do not need to perform the histogram search every frame.

Once we have the masks for the lines, we compare it to the binary image obtained before to identify the line pixels. These pixels are used to fit a second order polynomial on them using the `polyfit` function


![alt text][diag]


![diag_key]  





#### 4. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines # through # in my code in `my_other_file.py`

#### 5. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `yet_another_file.py` in the function `map_lane()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.
