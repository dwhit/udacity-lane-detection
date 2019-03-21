# **Finding Lane Lines on the Road** 


[//]: # (Image References)

[image1]: ./test_images_output/solidWhiteRight.jpg "Example Output image"
[gif1]: ./test_videos_output/challenge.gif "Challenge Video"
[image2]: ./test_images_output/curvy_road.jpg "Curved Road"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline is made up of 9 steps.

1. I use the cv2.inRange() function to filter out all non-white or yellow pixels. I determined the ranges for white and yellow empiracally via the MacOS Digital Color Meter tool.
2. I convert the remaining image to grayscale.
3. I blur the image with a Gaussian kernal of size 5.
4. I run the Canny edge detection algorithm on the image with a low of 50 and and high of 150 (so 1:3 ratio).
5. I crop out the region of interst using a trapezoid as my mask, making sure to not use hardcoded numbers, but ratios of the full image size. This helps deal with different sized images.
6. I perform a Hough transform on the resulting image, with the following parameters: 
    rho = 1, theta = 1 (converted to radians), threshold = 30, min_line_length = 20, max_line_gap = 10
7. I then pass the resulting lines from the Hough transform to the draw_lines() method, which filters and averages those lines to create a single estimate of each lane line.
8. To get a single estimate for each lane line, I made the following changes to draw_lines():
    a. I put all lines with a slope greater than 0.5 and on the left side of the image into one list. This list contains all my estimates of the left lane.
    b. I put all lines with a slope less than -0.5 and on the right side of the image into another list. This list contains all my estimates of the right lane.
    c. I then average each list of lines to get a single estimate for the left and right lane.
    d. I then extend each of those estimates so they run from halfway down the image to the bottom of the image.
9. I then use weighted_img() function to draw the estimates onto the input image.
    

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline

One potential shortcoming of this approach is its poor generalization to different lighting conditions. This can be seen in the challenge video, when the road goes into shadow and the asphalt surface changes. 

![alt text][gif1]

As you can see in the video, the lane estimates, especially the left one, fail to deal with the change. 

A second shortcoming is that this approach would not work with highly curved roads. As can be seen in the folowing example.

![alt text][image2]

The estimates are inaccurate as they are linearly averaging lines that are not actually in line with one another. Predicting a curve, and not just a straight line, would solve this issue.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to use opponent colors, or sample the color of the asphalt and use that sample to correct for the color of the lanes. That would address the first shortcoming I mention.

As I mentioned in the previous section, when the road is highly curved the estimates are inaccurate my pipeline is trying to fit a straight line onto a curve. Predicting a curve, and not just a straight line, would solve this issue.