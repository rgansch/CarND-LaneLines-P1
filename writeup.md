# **Finding Lane Lines on the Road** 


---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consists of the following steps:
**gaussian\_blur()**: Add Gaussian blur to image to remove noise
**canny()**: Find edges with Canny algorithm, I do this directly on the RGB image and do not convert it to Grayscale to improve effectivness on situations like challenge.mp4 where conversion to Grayscale looses too much information. By reduction from 3dimensional RGB to 1dimensional Grayscale the distinction of yellow lanes to asphalt gets lost since both have similar shades of gray.
**determine\_region\_of\_interest()**: Generate regions of interest converts a relative poly to the resolution of the image. This allows handling of videos with different resoultions like challenge.mp4. Fortunately, all images and videos work with the same relative poly coordinate set.
**region\_of\_interest()**: Mask region of interest applys the dynamically generated mask to the image
**hough\_lines()**: Hough transformation to find lines in the image
**seperate\_lines()**: Sort lines into right and left lane based on position and slope
**merge\_lines()**: Merge the lines to a single lane. Done by extrapoliting the lines to the the whole region of interest and averaging the start and end point coordinates with the initial lenght as weighting factor
**track\_lane()**: To improve stability for each lane a Kalman filter is used to track the lanes. reset\_filters() has to be called to reset the filter states before analyzing a new image/movie

For debugging purposes all images of the pipeline are pushed to **image\_pipeline** and printed out. This also allows to create movies for each pipeline step by modifying **process\_image** to the respective pipeline index.

### 2. Identify potential shortcomings with your current pipeline

1) The region of interest is determined statically, if lanes are outside of the region they will not be detected.

2) The discrimnation of lines into left and right lane (**sperate\_lines**) makes the pipeline robust against artefacts. However, lanes at arbritrary angles (e.g. horizontal crossing) can not be detected with this approach.

3) As can be observed in challenge.mp4 the pipeline can not work with curved lanes.


### 3. Suggest possible improvements to your pipeline

1) A possible improvement is to dynamically generate the region of interest. For this the starting points of the lanes could be acquired by analyzing the last row of the image. The horizon could be determined by the change of color.

2) For a more flexible lane detection a more sophisticated object recognition and tracking algorithm can be used

3) The only ideas i have for curved lanes are to use a image segmentation along the y direction for a piecewise linear approximation. Initially i assumed Hough transformation could be easily extended from y = mx + d to a quadratic equation y = qx^2 + mx + d (3 dimensional Hough space with q, m, d). However, I could not find any information to this approach.
