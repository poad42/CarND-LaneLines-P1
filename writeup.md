#**Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

[//]: # (Image References)

[image1]: ./examples/Grayscale_Image.png "Grayscale"
[image2]: ./examples/Gaussian_Blurred_Image.png "Gaussian Blur"
[image3]: ./examples/Canny_Edge_Detection_Image.png "Canny Detection"
[image4]: ./examples/ROI_Masked_Image.png "Region of Interest Mask"
[image5]: ./examples/Hough_Lines_Detected_Image.png "Hough Transform"
[image6]: ./examples/Hough_Lines_Processed_Image.png "Average/Extrapolated Image"
[image7]: ./examples/Final_Lane_Detected_Image.png "Lane Detected"

---

### Reflection

###1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My Pipeline consisted of 5 steps as explained below:

1. First, I converted the image to grayscale and further applied gaussian blur of kernel size 5.
![alt text][image1]

![alt text][image2]

2. Then I applied canny transform on the gaussian blurred image to identify the edges 
with high intensity gradient pixels on the image and remove the low intensity gradient pixel.

![alt text][image3]

3. Then I defined a polygon (hardcoded for a certain region) focussing on a specific region of lanes shown in test_images
Plotted this polygon on an empty frame coloring the edges in this region and then masking this frame onto the canny edge image.
This provided an output of edges on the frames focussing only on the lanes to be processed.

![alt text][image4]

4. Then I applied Hough transform on the canny edge masked output frame to convert the edges into the lines.

![alt text][image5]

Since these lines were showing multiple lines drawn due to multiple edges conversion so i applied further processing on the output.

In order to draw a single line on the left and right lanes I modified the draw_lines() function by creating 2 different arrays for left and right lane co-ordinates.

For each set of co-ordinates of a line i identified the slope of the line and based on postive or negative slope i categorised the co-ordinates into left lane co-ordinate(negative) or right lane co-ordinate (postitive slope). 

Then i created another function called fit_line() which took these arrays as an input along with pre-defined y co-ordinates of the final line.
In this function i created 2 more arrays for each x & y axis co-ordinates for individual lane calculation. 

For each set of x1,y1,x2,y2 co-ordinates of input array (left or right) i appended the x co-ordinates to x_array and y co-ordinates to y_array.
With these arrays i applied numpy polyfit functions to define the average slope and intercept of the line.
With available slope and intercept values i deduced the x co-ordinates for the line by putting the values in the linear equation of line
i.e., y = mx + b --> x = (y - b) / m

Now i have final x1,y1,x2,y2 co-ordinates of line on each lane to be plotted on original image.
I returned these values from fit_line() function to draw_lines() function where i drew the lines on canny edge image.

![alt text][image6]

5. Then i used add_Weighted function from opencv to blend the grayscale output of hough transform with the original color image having predefined weights for both the images.
This produced the output colored image with left and right lanes marked on it as the final output.

![alt text][image7]


###2. Identify potential shortcomings with your current pipeline

Following are the potential shortcomings with the current pipeline:

1. Since the region of interest polygon is hardcoded the pipeline might not work for different type of lane on an image.

I have tested some images from web with different lane angles and curves where the pipeline does not show data accurately.

2. Another shortcoming could be since the hough transform parameters and canny detection thresholds are hardcoded as per current set of images,
so this pipeline might not properly work for following type of image/video inputs
	a. different whether conditions (rainy/ foggy etc.). 
	b. evening/night time images.
	c. different types of roads (cemented/ sections/ cracks etc.).

I have tested for some images from web with different whether conditions, road conditions and night times which does not show accurate data with current pipeline.


###3. Suggest possible improvements to your pipeline

Following are the possible improvements that can be incorporated to current pipeline:

1. A possible improvement would be to refine the region of interest to cover all different types of lane inputs in a frame.

2. Another potential improvement could be to tweak the canny/hough parameters and average/extrapolate functions to detect different types of images/videos as specified in the potential shortcoming section.

