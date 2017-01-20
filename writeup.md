#Advanced Lane Finding Project

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
[image1]: ./results/draw_chess_points.png "Chess board points"
[image2]: ./results/undistorted.png "Undistorted"



[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[video1]: ./project_video.mp4 "Fit Visual"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

###Camera Calibration

####1. Have the camera matrix and distortion coefficients been computed correctly and checked on one of the calibration images as a test?
The code for this step is contained in the first code cell of the IPython notebook located in "./CarND-Advanced-Lane-Lines/calibration.ipynb.


I started by reading all the calibration images given for the calibration of the camera. I applied the function ***cv2.findChessboardCorners*** to each image to detect and obtain the coordinates of the chessboard corners, this function will return 2 values one boolean (retval), True if the corners were detected and a list(corners) . If the coordinates were detected I would append this coordinates to my list called `imgpoints` which is a list to save the points corresponding to chessboard corners on the image in a 2D plane. In order to be able to map the position of the all the corners found in each image a list called `objpoints` this list contains a so called "object points" that have the form of (x,y,z), where z is always zero, this object points represent the coordinates of the chesboard cornes in the real world. 

In order to make sure that the corners were detected correctly I used the function ***cv2.drawChessboardCorners*** to draw the corner found on top of the image tested. This is the result.

![alt text][image1]

I used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the ***cv2.calibrateCamera()*** function.  I applied this distortion correction to the test image using the ***cv2.undistort()*** function and obtained this result: 

![alt text][image2]


###Pipeline (single images)

####1. Has the distortion correction been correctly applied to each image?
To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]
####2. Has a binary image been created using color transforms, gradients or other methods?
Oooh, binary image... you mean like this one?  (note: this is not from one of the test images)

![alt text][image3]

####3. Has a perspective transform been applied to rectify the image?

The code for my perspective transform includes a function called `warper()`, which appears in lines 1 through 8 in the file `example.py` (output_images/examples/example.py) (or, for example, in the 3rd code cell of the IPython notebook).  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```
src = np.float32(
    [[(img_size[0] / 2) - 55, img_size[1] / 2 + 100],
    [((img_size[0] / 6) - 10), img_size[1]],
    [(img_size[0] * 5 / 6) + 60, img_size[1]],
    [(img_size[0] / 2 + 55), img_size[1] / 2 + 100]])
dst = np.float32(
    [[(img_size[0] / 4), 0],
    [(img_size[0] / 4), img_size[1]],
    [(img_size[0] * 3 / 4), img_size[1]],
    [(img_size[0] * 3 / 4), 0]])

```
This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 585, 460      | 320, 0        | 
| 203, 720      | 320, 720      |
| 1127, 720     | 960, 720      |
| 695, 460      | 960, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

####4. Have lane line pixels been identified in the rectified image and fit with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

####5. Having identified the lane lines, has the radius of curvature of the road been estimated? And the position of the vehicle with respect to center in the lane?

Yep, sure did!

---

###Pipeline (video)

####1. Does the pipeline established with the test images work to process the video?

It sure does!  Here's a [link to my video result](./project_video.mp4)

---

###README

####1. Has a README file been included that describes in detail the steps taken to construct the pipeline, techniques used, areas where improvements could be made?

You're reading it!


---
##Discussion

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  

