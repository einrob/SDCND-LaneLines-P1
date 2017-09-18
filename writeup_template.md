# **Finding Lane Lines on the Road - Writeup** 


---

**Finding Lane Lines on the Road**

The goals of this project are the following:
* Implement a pipeline that finds lane lines on the road surface using canny edge detection and hough transformation 
* Try to filter detection to stabilize lane estimation 

---

### Reflection

### 1. Implemented pipeline

Following steps are performed on each input image of the image stream: 

* Convert input image to grayscale
* Blur grayscale image to reduce high frequency image information (noise or smaller gradients) which helps canny to detect only strong gradients 
* Apply canny edge detection algorithm on the blurred grayscale image 
* Corp edge image to ROI infront of the car 
* Apply hough line detection on remaining canny edge image 
* Iterate through all detected lines and calculate slope
* Distinguish between left line and right line using calculated slope (left = positive slope, right = negative slope)
* Average line slopes for left and right line using the Welford online algorithm (https://en.wikipedia.org/wiki/Algorithms_for_calculating_variance)
* Using y=kx+d line equation to do calculations for approximated lines 
* Calculate intersection with y-Axis d (left border of image) for left and right line using averaged line positions and averaged slope of the lines found by hough line detector 
* Calculate coordinates of intersection of estimated left and right line with bottom edge of image (to know where I have to start to draw the line from)
* Calculate coodinates of intersection of estimated left and right line at specific image height (to know where I have to end drawing of the lines)
* Drawing estimated lines using draw_lines function at calculated start and end coordinates on a blank copy of the input image 
* Overlay image with estimated lines and input image for visualiziation using weighted_img function 
* Return overlayed image 

I tried to stabilize the line slopes and intersection points of the calculated lines with the y-axis with 1D-Kalman filters. The 1D-Kalman filter was introduced in Udacity course https://classroom.udacity.com/courses/cs373/lessons/48723604/concepts/487236030923 where the filter took a motion for the prediction step. I assume that the slope and the y-axis intersection points of the detected lines are constant or at least do not change rapidly, so I added 0.0 at the prediction step and only increased the uncertainty. The added plots show calculated and filtered slopes and d values of the lines. I empirically tuned the uncertainties so that the most of the noise is smoothen out by the filters. 


### 2. Shortcomings of pipeline 

* Lines can only represent straightline marking geometries on a flat surface accurately 
* Fixed ROI size does not adapt to curves, hills and ditches (change of geometry of lane markings when pitch angle of the camera changes relative to the ground plane) 
* Fixed ROI position does not reflect lateral movement of the car - car is not always moving exactly in the middle of the lane
* The fixed ROI was shortened to compensate camera pitch movement - this leads to loss of valuable edge data of the lane markings in regions further ahead 
* ROI includes a large image area between the lane markings - detections in this area are not very useful for the lane geometry approximation 
* Fixed parameters for canny edge detection and hough transformation only apply to this test set of images 
* Generally this pipeline is trimmed to work with the given test set of images and surely is going to fail with other test sets with different weather, light and traffic conditions 
* Only rudimentary plausibility checks are made on detected lines  
* Lines are represented with y = kx+d which can lead to numerical problems when horizontal or vertical lines appear

### 3. Suggest possible improvements 

* Change line representation to polar coordinates if possible 
* If no line is detected for left OR right line - use mirrored line geometry of the detected stable one 
* If no line is detected at all - use last detections for n-frames then reset  
* Check if detected lines for left OR right OR booth are unplausible - maybe check if detected slopes are inside of 2 sigma (95,45% of all detections)
* Changing ROI size and geometry according to the geometry of the detected lines - maybe using the estimated line slopes
* Changing ROI position to compensate lateral movement of the car - align ROI to an estimated middle line 
* To be able to represent the line marking geometry more accurately it is necessary to tile the input image into several horizontal segments. For each horizontal segment a line position and slope can be estimated. 
* Use polynomial approximation of the lane marking geometries
