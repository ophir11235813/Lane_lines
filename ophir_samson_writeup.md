# **Finding highway lane lines – base and challenging case** 

## Base case: finding lane lines on a straight, sunny, shadow-less, constant-surface road with clear markings

---

Goal of this case: Identify and highlight the left- and right-lane markings of a highway, given a raw video feed.

Specifically: 
* Build a pipeline that receives a still image of a highway and finds the lane lines
* Reflect on the process of building this code.


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

![ScreenShot](https://raw.github.com/ophir11235813/Lane_lines/master/draw_lines.jpg = 72x128)

STEP 8: Overlay the lines with the original image. 

![ScreenShot](https://raw.github.com/ophir11235813/Lane_lines/master/final.jpg)


![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
