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
[image0]:  ./output_images/before_calibration.jpg
[image1]:  ./output_images/after_calibration.jpg
[image2]:  ./output_images/undistorted.jpg
[image3]:  ./output_images/binary.jpg
[image4]:  ./output_images/transform.jpg
[image5]:  ./output_images/fit.jpg
[image6]:  ./output_images/test1.jpg
[image7]:  ./output_images/test2.jpg
[image8]:  ./output_images/test3.jpg
[image9]:  ./output_images/test4.jpg
[image10]: ./output_images/test5.jpg
[image11]: ./output_images/test6.jpg
[image12]: ./output_images/straight_lines1.jpg
[image13]: ./output_images/straight_lines2.jpg
[image14]: ./output_images/original_test1.jpg
[image15]: ./output_images/original_test2.jpg
[image16]: ./output_images/original_test3.jpg
[image17]: ./output_images/original_test4.jpg
[image18]: ./output_images/original_test5.jpg
[image19]: ./output_images/original_test6.jpg
[image20]: ./output_images/original_straight_lines1.jpg
[image21]: ./output_images/original_straight_lines2.jpg
[video1]:  ./project_video_output.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

There are two main steps to this process: use chessboard images to obtain image points and object points, and then use the OpenCV functions cv2.calibrateCamera() and cv2.undistort() to compute the calibration and undistortion.

The code is available in function cal_undistort in IPython notebook ./P2.ipynb. 

- before calibration
![alt text][image0]

- after calibration
![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

- original photo
![alt text][image14]

- distortion correction
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image. 

cv2.Sobel() is used for to calculate gradient and cv2.cvtColor(..., cv2.COLOR_RGB2HLS) is used for HLS color space conversion. 

The code is available in function binary_image in IPython notebook ./P2.ipynb.

- binary image
![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

I use cv2.getPerspectiveTransform() to get M, the transform matrix and use cv2.warpPerspective() to apply M and warp your image to a top-down view. 

The source and destination points to getPerspectiveTransform is hard-coded as below:
	
src = np.float32(
	[[(img_size[0]/2) - 55, img_size[1]/2 + 100],
	[((img_size[0]/6)-10), img_size[1]],
	[(img_size[0]*5/6)+60, img_size[1]],
	[(img_size[0]/2+55), img_size[1]/2+100]])

dst = np.float32(
	[[(img_size[0]/4), 0],
	[(img_size[0]/4), img_size[1]],
	[(img_size[0]*3/4), img_size[1]],
	[(img_size[0]*3/4), 0]])
	
The code is avilable in function perspective_transform in IPython notebook ./P2.ipynb.

- perspective transform
![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I use the two highest peaks from histogram as a starting point for determining where the lane lines are, and then use sliding windows moving upward in the image to determine where the lane lines go.

The code is avilable in function find_lane_pixels in IPython notebook ./P2.ipynb.

- Identify lane line
![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The code to calucalte the radius of curvature is available in function cal_lane_curv in IPython notebook ./P2.ipynb.

The code to calucalte position of the vehicle with respect to center is available in function cal_offset in IPython notebook ./P2.ipynb.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The code is available in function draw_figure in IPython notebook ./P2.ipynb.

- final image
![alt text][image6]

#### 7. All results for test images:

- test1.jpg
![alt text][image14]
![alt text][image6]

- test2.jpg
![alt text][image15]
![alt text][image7]

- test3.jpg
![alt text][image16]
![alt text][image8]

- test4.jpg
![alt text][image17]
![alt text][image9]

- test5.jpg
![alt text][image18]
![alt text][image10]

- test6.jpg
![alt text][image19]
![alt text][image11]

- straight_lines1.jpg
![alt text][image20]
![alt text][image12]

- straight_lines2.jpg
![alt text][image21]
![alt text][image13]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Problem:
- the video pipeline is a bit slow, not applicable for real-time scenarios such as autonomous driving
- other objects like road edge, shadow boundary, can be wrongly recognized as lane line and the sanity check fails in such case
- the hardcoded source and destination for perspective transform is not good and may fail in some scenarios

Improvements:
- object detection using computer vision can be slow and ML methods may help improve the speed.
- the current sanity check only checks slope difference and distance difference between two lines. It can be improved by considering other factors such as curvature, offset
- Smoothing methods can be implemented by taking an average over some past measurements
