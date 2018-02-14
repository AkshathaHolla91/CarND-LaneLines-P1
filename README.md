# **Finding Lane Lines on the Road** 

## **Submission Files:**
* [Jupyter Notebook](https://github.com/AkshathaHolla91/CarND-LaneLines-P1/blob/master/P1.ipynb)
---

## Writeup
---

**Finding Lane Lines on the Road**

The goals / steps of this project were the following:
* Make a pipeline that finds lane lines on the road
* Reflect on my work in a written report


[//]: # (Image References)

[grayscale]: ./writeup_images/grayscale.png "Grayscale"

[blur1]: ./writeup_images/blur1.png "Blur 1"

[cannyedge]: ./writeup_images/cannyedge.png "Canny Edge"

[blur2]: ./writeup_images/blur2.png "Blur 2"

[roi]: ./writeup_images/roi.png "ROI"

[houghv1]: ./writeup_images/hough_v1.png "Hough Lines"

[weightedv1]: ./writeup_images/weighted_v1.png "Weighted Sum"

[houghv2]: ./writeup_images/houghlines.png "Hough Lines Extrapolated"

[weighted]: ./writeup_images/weighted.png "Weighted after Extrapolation"

---

### Reflection

### 1. Pipeline Description

My pipeline consisted of 7 steps.

1. The first step was to convert the image to grayscale (Reducing the number of channels from 3 to 1 ) in order to reduce the amount of computations.

![alt text][grayscale]

2. The second step was to apply Gaussian blur with kernel size of 5 in order to reduce the noise in the image

![alt text][blur1]

3. The third step was applying Canny edge detection to detect edges based on the high and low thresholds which I had set to 65 and 195 respectively maintaining the suggested 1:3 ratio. This enusered that only the necessary edges were detected.

![alt text][cannyedge]

4. The fourth step was to apply an additional level of gaussian blur so that the lines are more defined after edge detection

![alt text][blur2]

5. The next step was defining the Region of interest. Here assuming that the front facing camera that took the image is mounted in a fixed position on the car we can define a region where the lane lines are most likely to appear. This was done by using a polygon where the height  was set to approximately 60% of the image and the widths starting from 80% of the image at the bottom decreasing upto 10% at the top according to the observed lane lines.

![alt text][roi]

6. The sixth step would be to apply Hough transform to find lines from the masked region of interest. The values provided to the hough transform were distance rho=1 ,angular resolution theta=pi/180 ,threshold of 60 , minimum line length of 100 and maximum line gap of 50 to detect the lines properly. The lines are then plotted using given draw lines function.

![alt text][houghv1] 

7. The seventh step would be to superimpose the output of the hough lines function with the original image to produce the weighted sum and thereby draw the detected lane lines on the image

![alt text][weightedv1] 


In order to draw a single line on the left and right lanes, I have created a new  draw_lines_with_extrapolation() function with the following steps
* Calculate the slope m and intercept b for all lines.
* If slope is greater than zero then consider it as a right lane line ,otherwise consider it as left lane line.
* Calculate the average slope and intercept for both the right and left lane separately.
* Using the average slope and intercept calculated , the x,y co-ordinates of the line starting point and ending point on each lane is calculated and consolidated lane lines are drawn in the right and left lane.

For drawing the lane lines on the challenge video the  condition for classifying the lines was further modified to put lines with m > 0.5 to right lane and  m < -0.5 to the left lane so that unnecessary lines were not added while averaging.

![alt text][houghv2] 


![alt text][weighted]



### 2. Identify potential shortcomings with your current pipeline


1. One potential shortcoming would be that when the  lane lines are curved as seen in the challenge video , the line drawn by the above approach would not follow the curved path.

2. Another shortcoming is that edges are not detected when moving between areas with varying light intensities and hence the lines are not properly drawn in those regions where it is not able to differentiate between bright areas and light coloured lane lines.



### 3. Suggest possible improvements to your pipeline

1. A possible improvement would be to to maintain a running average of  values whenever an edge is not detected in a particular lane so as to draw a line and maintain lane line continuity

2. Another potential improvement could be to draw curved lane lines to adapt to the curvature of the roads/lanes.

3. The intersection of lane lines due to the curvature of roads can be avoided by separating the masked region to left and right segments and then processing them separately
