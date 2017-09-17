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

* Converted input image to grayscale 
* Blur grayscale image to reduce high frequency image information (noise or smaller gradients) which helps canny to detect only strong gradients 
* Apply canny edge detection algorithm to the blurred grayscale image 
* Apply hough line detection on edge image 
* Iterate through all detected lines and calculate slope
* Distinguish between left line and right line using calculated slope (left = positive slope, right = negative slope)
* Average line slopes for left and right line using the Welford online algorithm (https://en.wikipedia.org/wiki/Algorithms_for_calculating_variance)
* Using y=kx+d line equation to do calculations 
* Calculate intersection with y-Axis d (left border of image) 
* Calculate coordinates of intersection of estimated left and right line with bottom edge of image
* Calculate coodinates of intersction of estimated left and right line at specific image height (neat corping of lines)
* Drawing estimated lines using draw_lines function at calculated coordinates on a blank copy of the input image 
* Overlay image with estimated lines and input image for visualiziation using weighted_img function 

To stabilize 


### 2. Shortcomings of pipeline 

* Lines can only represent straight line marking geometries accurately 
* Fixed ROI size does not adapt to curves, hills and ditches (change of geometry of lane markings) 
* Fixed ROI position does not reflect lateral movement of the car 
* The fixed ROI was shortened to compensate camera pitch movement - this leads to loss of valuable edge data of the lane markings in regions ahead 
* Fixed parameters for canny edge detection and hough transformation only apply to this test set of images 
* Generally this pipeline is trimmed to work with the given test set of images and surely is going to fail with other test sets with different weather and light conditions 
* Only rudimentary plausibility checks are made on detected lines  


### 3. Suggest possible improvements 

* If no line is detected for left OR right line - use mirrored line geometry of the detected one 
* If no line is detected at all - use last detections for n-frames then reset or set detection to invalid 
* Check if detected lines for left OR right OR booth are unplausible - check if detected slopes are inside of 2 sigma (95,45% of all detections)
* Changing ROI size and geometry according to the geometry of the detected lines - eventually using the estimated line slopes to calculate horizon 
* Changing ROI position to compensate lateral movement of the car - align ROI to an estimated middle line 
* To be able to represent the line marking geometry more accurately it is necessary to tile the input image into several horizontal segments. For each horizontal segment a line position and slope can be estimated. Then a clothoid curve (curve with linear variation of curvature) could be fitted into the calculated points.
