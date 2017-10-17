
# **Project 1 - Finding Lane Lines on the Road** 

* By: Carmelo Malara
* Date: 16/10/2017

* Project: Project 1 - Finding Lane Lines on the Road
* Course: Udacity's Self-Driving Car Nanodegree Program (Start: 05-Oct-2017) 

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

### Supporting Files

This project's supporting jupyter notebook (P1.ipynb) has been uploaded to the following git repository:
https://github.com/cmalara/CarND-LaneLines-P1.git

[//]: # (Image References)

[image1]: ./test_images_output/GRAY_solidWhiteCurve.jpg "Grayscale"
[image2]: ./test_images_output/Gaussian_solidWhiteCurve.jpg "Gaussian Blur"
[image3]: ./test_images_output/Canny_solidWhiteCurve.jpg "Canny Edge"
[image4]: ./test_images_output/Mask_solidWhiteCurve.jpg "Masked Region"
[image5]: ./examples/line-segments-example.jpg "Line segments"
[image6]: ./test_images_output/solidWhiteCurve.jpg "Hough Transformed and Lines Drawn"

---

### Reflection

### 1. My pipeline consisted of 5 steps

#### Step 1. Convert image to Gray Scale

I converted the images to grayscale using cv2.cvtColor(img, cv2.COLOR_RGB2GRAY) function.  The converted image looks like the one below.

![alt text][image1]


#### Step 2. Apply a Gaussion Blur

I then applied the Gaussian Blur function cv2.GaussianBlur(img, (kernel_size, kernel_size), 0).  Theoretically speaking I am led to believe that this step is optional since the next step (3) the Canny Edge detector function applies, by default, a 5 x 5 Gaussian.  This Gaussian smoothing of the image is a way of suppressing noise and spurious gradients by averaging over a large area.  The Kernel has to be an odd number.  I am not sure why for now, but will read up on it.  The processed image looks like the one below.

![alt text][image2]

#### Step 3. Run the Canny Edge Detector

I applied the Canny Edge detector function cv2.Canny(img, low_threshold, high_threshold) to detect strong edges, i.e. strong edges are where the pixel colour gradient is sharp (falling between the high and low thresholds).  The processed image looks like the one below.

![alt text][image3]

#### Step 4. Masking off the main region of interest (i.e. where we believe the lines to be)

I applied a mask over a trapezoidal region of interest using both the cv2.fillPoly(mask, vertices, ignore_mask_color) function and the cv2.bitwise_and(img, mask).  This second function keeps only the region of interest as can be seen in the processed image below.

![alt text][image4]

#### Step 5. Hough Transform to detect the lines then draw them

I identified the lines using the Hough transform function cv2.HoughLinesP(img, rho, theta, threshold, np.array([]), minLineLength=min_line_len, maxLineGap=max_line_gap).  I then created a canvas the same size as the image using the function np.zeros((img.shape[0], img.shape[1], 3), dtype=np.uint8), and finally drew the lines.

My initial output looked like the image (below) after detecting line segments using the helper functions provided cv2.line(img, (x1, y1), (x2, y2), color, thickness)

![alt text][image5]

The goal was to connect/average/extrapolate line segments. In order to draw single extrapolated lines on the left and right lanes, I modified the draw_lines() function in line with an example I borrowed (and later modified for the video challenge to include a y-intercept term to fix the bottom of the left line and the top of the right line to predefined regions of interest - not ideal but effective for this short challenge) from Kevin Kuprinski's fork of the CarND-LandLines-P1 project to produce the output shown below.

![alt text][image6]



### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when road bends to the left or right.  I have a feeling that the lines would either disappear or describe an unstable region of interest.  It is important that where the system predicts the lines to exist remains stable over a drive-cycle.

Another shortcoming could be if the lighting changed to cast shadows on the road as was seen in the challenge video, such that the left line collapsed on itself.  This prompted the modification to the draw_lines() function to include a y-intercept term.  However this in itself would have limited practical application.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to create multiple zones of interest, like stacked trapeziums one infront of the other where we draw shorter line segments that link with the one below and above it.  This should hopefully stabilise the line and stop it flailing about as shadows or colour changes occur on the road.
