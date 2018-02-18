# **Finding Lane Lines on the Road** 

## Nick Xydes

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images_output/solidWhiteRight.jpg "Results"

---


### Results


Please find my results in the test_images_output and test_videos_output folders. The code to generate those can be found in the P1.ipynb file.

---

### Reflection

### 1. Lane Finding pipeline

In order to find and draw the lane lines on the image, I did the following steps:
1. Read each raw image or frame
2. Convert each image to a grayscale image
3. Apply a Gaussian smoothing pattern to "blur" the image, this will get rid of noisy artifacts which may override the lines I want
4. Apply a canny transform, this transforms the image into a gray scale image of all the edges where colors change
5. Apply a mask over the image. Really, I know the lane lines are going to be in the bottom middle so lets only examine this portion of the frame (unless the car tips over, then you've got other problems)
6. Apply a hough transform to the image. This will find all the line segments of x length (and other parameters) in the edge image


*The above is entirely based on the tutorials, the below is additional logic by me*


7. I iterate through each line in the hough transform and sort them into right/left lane candidates based on their slope
8. I throw out any right candidates that are on the left side of the image and vice versa
9. Then, I filter the remaining lines. To do this I calculate the mean and standard deviation of both the slope and y-intercept for all lines and throw out those greater than 1.5 standard deviation away from either
10. I apply a linear fit to the left over lines to determine the best solid line.
11. Lastly, while applying the linear fit, I extend the line from the bottom of the image to the bounding box in the middle


Steps 7-11 are what I added to the draw_lines() function in order to draw a single line down the lane marker

The end result:

![alt text][image1]


### 2. Potential shortcomings with my current pipeline


My current pipeline has a number of assumptions that would not hold in the real world:
* I assume the left lane is on the left side and the right lane on the right. This would not hold when we change lanes
* I apply a linear fit to the resulting lane marker, this does not hold as the car goes around curves
* This vision architecture is going to latch onto the edges in the images, it will be hard to determine on dirty / damaged roads where the lane is and where cracks might be.
* If cars merge in front of us, we will lose the lane visually and it will disappear


### 3. Possible improvements to my pipeline

I can think of a number of possible improvements.

First, I could do more filtering when I first look at the lines coming from the Hough transform. I know that the lines all need to be roughly the same slop and within a certain range. This would get rid of horizontal line false positives


Second, I should group the lines in a smart way based on their y-intercept and slopes. I would then apply a cost function based on cumulative distance of all the lines in the group to choose which group is the actual line.


Third, we could make some assumptions about the lane markings, such as they should always be 8-12 feet apart (some # of pixels at the bottom of the image), this would help with choosing the group as well.


Furthermore, it would be good to maintain knowledge about the lane markers from frame to frame. Using a kalman filter or similar we could get rid of noise and maintain better tracking.


Finally, we should detect for occlusions when cars cut us off and maintain a history of where we think the line is, once we are better and can detect cars.



