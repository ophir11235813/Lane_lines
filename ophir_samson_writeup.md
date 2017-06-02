# **Finding highway lane lines – base and challenging case** 

---

In both cases, the goal is to identify and highlight the left- and right-lane markings of a highway, given a raw video feed.

Specifically: 
* Build a pipeline that receives a still image of a highway and finds the lane lines
* Reflect on the process of building this code.


## 1. Base case: finding lane lines on a straight, sunny, shadow-less, constant-surface road with clear markings


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

Introduction to method: We'll first show how to do this for a single image. The video is then a stream of many independent images, each of which are processed using the same method. 

STEP 1: Read in image (a three color channel image), and convert to gray scale (single channel). 

![ScreenShot](https://raw.github.com/ophir11235813/Lane_lines/master/grayscale.jpg)

STEP 2: Apply Gaussian smoothing to the gray scale image. This applies a Gaussian blur over a filter, thus reducing noise and unneccessary detail in the image. 

STEP 3: Run a Canny Edge Detection algorithm over the smoothed image. This algorithm looks for steep changes (i.e. high value derivatives) in color functions, thus highlighting where the edges of objects are. 

![ScreenShot](https://raw.github.com/ophir11235813/Lane_lines/master/canny_edge.jpg)

STEP 4: Define a "region of interest". We are interested only in the region in front (and a little to the sides) of the car, rather than in the entire image. Hence define a trapezoid within which we subsequently detect the lanes. Note that this trapezoid can be thought of as a 2D-projection on to the camera of a rectangle that is placed on the road.

STEP 5: Perform a Hough Transformation to find the start- and end-points of continuous lines in the image. Note that this works by mapping each point in the image space (x,y cartesian coordinates) onto a (sine) curve in the Hough space (r,\theta polar coordinates). Therefore, the point in Hough space where N sine curves intersect defines the line that connects the corresponding N points in cartesian space. 

STEP 6: Find lane markings. To do this, measure the gradient of the lines that connect each set of 4 points (from the Hough Transformation in step 6). Then, the left lane markings can be identified as being the points that have an associated NEGATIVE gradient, while the right lane markings will have POSITIVE gradients. Note that the unconventional sign change is because the y-values, or height, of left lanes decreases as the x-values increases. 

Next, separate the lines in two sets (left- and right-lanes sets). From each set, find the (x,y) coordinates of both the closest and furthest point to/from the car, and draw a line between those points using the straight line equation y = mx + c. 

Finally, extrapolate the line to the bottom of the image, and to the top of the region of interest using the same straight line equation. 

This will result in two sets (one for each lanes) of 4-tuples, with each 4-tuple defining the start- and end-points of the extrapolated lines.

STEP 7: Draw the lines, using different colors. 

![ScreenShot](https://raw.github.com/ophir11235813/Lane_lines/master/draw_lines.jpg)

STEP 8: Overlay the lines with the original image. 

![ScreenShot](https://raw.github.com/ophir11235813/Lane_lines/master/final.jpg)


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...


## 2. Challenge: finding lane lines on a curved highway, with shadows and variable-surfaces with (some) unclear markings

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

Preparation: Camera distortion

A few prepapatory steps before we begin the main pipeline. Firstly, we must correct the camera distortion that occurs as a result of the curved. We do this by considering the camera's image of a shape with known geometry (chessboards at different angles), and comparing various points in real space (corners of the chessboard) versus where the camera images them to be. This gives us a "camera matrix" and a list of "distortion coefficients" which are specific to the camera that takes in the raw images (i.e. the camera at the front of the car). 

This is important because for this exercise, we will need to transform the raw image to bring the furthest points from the car "closer" before processing. Any camera distortions here will be magnified by this transformation, and so we need to correct the distortions up front. 

![ScreenShot](https://raw.github.com/ophir11235813/Lane_lines/master/calibration2.jpg)

#### Pipeline:

STEP 1: Undistort the raw image, using the distortion coefficients and the camera matrix (as explained above).

STEP 2: Transform the (undistorted), so that we magnify the furthest points from the car. 

STEP 3: Run the code to find the (x,y) coordinates of the left and right lanes in this transformed image. \n
-- STEP 3a: Map the (color) image to HLS space, and apply the first set of white and yellow filters to identify the left (yellow) and right (white) lines. This will result in a black image with two identified lines in HLS space.
-- STEP 3b: Convert to grayscale and constrain to a region of interest (trapezion, as in the straight-line example)
-- STEP 3c: Send to code that finds (x,y) coordinates of the lines in a grayscale image. *See below for further details of how this function works.*

STEP 4: Check whether enough points have been found in left and right lanes. If not, then run a second set of white and yellow filters. 

At this point, we will have the (x,y) coordinates of both lanes. 

STEP 5: Apply a quadratic polynominal fit to the points. This will give the (shallow) parabolas for the left and right lines. Extrapolate the parabolas to the lower and upper ends of the region of interest. 

STEP 6: Highlight the lane lines and fill the area in between with green shade. This area will still be in the transformed space. 

STEP 7: Transform the shaded area back to real space, using the opposite process to STEP 2. 

STEP 8: Overlay the lines and shaded area with the original image

#### Further details of STEP 3c: How to find (x,y) coordinates of white points in a grayscale image

This part of code accepts a grayscale image with some non-black pixels, representing the left and the right lines in the transformed space. It then finds  the (x,y) coordinates those pixels.

To do this, we separate the image into two halves (left half for the left-lane, and right half for the right lane). We then further "slice" each half into ~100 horizontal rows (y-coordinates). For each slice, we find the column (x-coordinate) with the highest average value: The more "white" there is, the more likely it is that the column (within that slice) is where the lane line is. 
    
