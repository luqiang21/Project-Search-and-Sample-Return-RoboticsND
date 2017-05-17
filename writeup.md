## Project: Search and Sample Return
### Writeup Template: You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---


**The goals / steps of this project are the following:**  

**Training / Calibration**  

* Download the simulator and take data in "Training Mode"
* Test out the functions in the Jupyter Notebook provided
* Add functions to detect obstacles and samples of interest (golden rocks)
* Fill in the `process_image()` function with the appropriate image processing steps (perspective transform, color threshold etc.) to get from raw images to a map.  The `output_image` you create in this step should demonstrate that your mapping pipeline works.
* Use `moviepy` to process the images in your saved dataset with the `process_image()` function.  Include the video you produce as part of your submission.

**Autonomous Navigation / Mapping**

* Fill in the `perception_step()` function within the `perception.py` script with the appropriate image processing functions to create a map and update `Rover()` data (similar to what you did with `process_image()` in the notebook).
* Fill in the `decision_step()` function within the `decision.py` script with conditional statements that take into consideration the outputs of the `perception_step()` in deciding how to issue throttle, brake and steering commands.
* Iterate on your perception and decision function until your rover does a reasonable (need to define metric) job of navigating and mapping.  

[//]: # (Image References)

[image1]: ./misc/rover_image.jpg
[image2]: ./calibration_images/example_grid1.jpg
[image3]: ./calibration_images/example_rock1.jpg
[image4]: ./misc/rock_result.jpg
[image5]: ./misc/grid_result.jpg

## [Rubric](https://review.udacity.com/#!/rubrics/916/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!
### First Rubric Item


![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of image before perspective transform and thresholding.

![alt text][image2]
![alt text][image3]

#### 2. Describe how (and identify where in your code) you used color thresholds to create a thresholded binary image.  Provide an example of a binary image result.
I used a combination of color thresholds to generate a binary image (thresholding steps at lines 20 through 34 and lines 77 through 94 in `perception.py`). Function color_thresh() returns the detected navigable region and obstacles. Function color_thresh_rock() returns a binary image of the golden rock by converting to hsv space and apply thresholding for yellow color. Here's an example of my output for this step on the two images above.

![alt text][image5]
![alt text][image4]


#### 3. Describe how (and identify where in your code) you performed a perspective transform

From lines 70 to 75, I apply a perspective transform on an image by selecting four points in the grid image, and creating four destination points that makes a square to obtain the transform matrix M. Then I use cv2.warpPerspective() to warp the image.


#### 4.Describe in your writeup how you modified the process_image() to demonstrate your analysis and how you created a worldmap.

Inside the process_image() function, I first transform the image to the bird's view, and then apply three thresholding to obtain binary images for navigable terrain, obstacles and rock samples. Use the three binary images to combine to get the final image showing the information we want.

For worldmap, I applied rotation and translation on the robot's location. And add the scaled navigable terrain pixels to the ground truth world map every time step.


---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I took some time to familiar with the Rover object and how it will update attributes every time step. The navigable area detection is only based on thresholding which is very specific to this simulator's light sand ground. The pipeline will easily fail if the texture of the ground changes like roads of other colors. And also for the golden sample, I applied yellow color recognition which is also very specific to this type of sample.

I added the variable stop_time that counts the time that robot has been stopped. If it has stopped for a while, even though it has navigable terrain, it should turn since very likely it stuck at a rock. But this method is not robust, more decisions should be made to avoid the robot stop at some unexpected positions. Instead of choosing the average angle every time, I give preference to left a little bit, thus the robot is more likely to explore every corner of the whole map.
