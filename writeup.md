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
[image3]: ./results/undis_example.png "Undistortion"
[image4]: ./results/abs_sobel_hsl.png "Combining thresholds"
[image5]: ./results/warped.png "Warped"
[image6]: ./results/histogram.png "Histogram"
[image7]: ./results/polinomy.png "Polynomial"


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
In order to show the results obtained once the calibration was obtained, I will show you the distortion correction aplied in one of the test images.

The function ***cv2.undistort(img, mtx, dist, None, mtx)*** takes an image, the calibration matrix, the distortion coefficients. The function returns an image without distortion. 

![alt text][image3]


####2. Has a binary image been created using color transforms, gradients or other methods?
In order to filter the images and keep only the lane lines, I used the absolute value of Sobel x since this will help me to keep more vertical lines and it will dimish the horizontal lines. This is helpful since the lane lines I'm looking for are vertical. Additilonally I also implemented a color thresholding using the HLS color space and applying a threshold on the S(saturation) channel since this will work for yellow and white lines.


![alt text][image4]



####3. Has a perspective transform been applied to rectify the image?

The function `warp` can be found in the IPython notebook called `Development` on the 10th cell. It takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  

I chose to hardcode the source and destination points in the following manner:

```
horizon = np.uint(2*image_size[0]/3)
bottom = np.uint(image_size[0])
center_lane = np.uint(image_size[1]/2)
offset = 0.2

x_left_bottom = center_lane - center_lane
x_right_bottom = 2*center_lane
x_right_upper = center_lane + offset*center_lane
x_left_upper = center_lane - offset*center_lane


source = np.float32([[x_left_bottom,bottom],[x_right_bottom,bottom],
				[x_right_upper,horizon],[x_left_upper,horizon]])

destination = np.float32([[0,image_size[0]],[image_size[1],image_size[0]],
                  [image_size[1],0],[0,0]])


```
This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 720, 0        | 720, 0        | 
| 1280, 720     | 720, 1200     |
| 768, 480      | 0, 1200       |
| 512, 480      | 0, 0          |


I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image5]


####4. Have lane line pixels been identified in the rectified image and fit with a polynomial?

After calculating the warped image. First I calculate the histogram across the image, this was implemented using `numpy.sum()` to sum all the pixels on the rows, the result is the following:

![alt text][image6]

Then I pass the histogram to a function `find_two_peaks_image` to find the center of each of the peaks. This code can be found on the IPython notebook called `Development` on cell #16, then I implemented a function called `sliding_window` to iterate through the image and find the x coordinates and y coordinates of the pixels that correspond to the lanes starting from the centers that I found earlier.

Once I got the pixels I fit them to a 2nd order polynomial of the form:
				`f(y)=Ay^2 + By + C ` 

The result obtained was this for one of the test images:

![alt text][image7]


####5. Having identified the lane lines, has the radius of curvature of the road been estimated? And the position of the vehicle with respect to center in the lane?

Yes. I used the x and y coordinates obtained with the polynomial to calculate the curvature of the lane. This code can be found on the IPython notebook called `Development` on cell #6.


---

###Pipeline (video)

####1. Does the pipeline established with the test images work to process the video?

The code used for the final pipeline can be found on the IPython notebook called `LaneDetection`, this notebook will contain all the function described on point above but with out the images demostrations. 

Aditionally contains the code for the Line class that was implemented to keep track of the lanes in each frame.

Here's a [link to my video result](https://youtu.be/Vn8J9WvfpgY)

---

###README

####1. Has a README file been included that describes in detail the steps taken to construct the pipeline, techniques used, areas where improvements could be made?

This is the README of this project.


---
##Discussion

  
This project was very interesting and challenging. I learnt a lot of technics used for computer vision like measuring and correcting distortion, perpective trasnformation, how to use diffent color spaces to filter images, etc. One of the most challenging parts for me was how to stablish the right parameters to determine which detected line was not a good one so sometimes I added lanes that messed up the avegerage of the lane. So definitely I should work on a better strategy or on a more sophisticated method to identified bad lines. I also could explore more on the threshold and color spaces to find a better filter.
  
