# **Finding Lane Lines on the Road - Writeup** 


---

**Finding Lane Lines on the Road**

The goals of this project are the following:
* Implement a pipeline that finds lane lines on the road using canny edge detection and hough transformation 
* Try to filter detection to stabilize lane estimation 

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. First, I converted the images to grayscale, then I .... 

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by ...

If you'd like to include images to show how the pipeline works, here is how to include an image: 

### 2. Shortcomings of pipeline 

* Lines can only represent straight line marking geometries accurately 
* Fixed ROI size does not adapt to curves, hills and ditches (change of gemetry of lane markings) 
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
