# **Finding highway lane lines – base and challenging case** 

## Base case: finding lane lines on a straight, sunny, shadow-less, constant-surface road with clear markings

---

Goal: Identify and highlight the left- and right-lane markings of a highway, given a raw video feed.

Introduction to method: We'll first show how to do this for a single image. The video is then a stream of many independent images, each of which are processed using the same method. 

Method:

STEP 1: Read in image (a three color channel image), and convert to gray scale (single channel). 

STEP 2: 


* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. First, I converted the images to grayscale, then I .... 

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by ...

If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
