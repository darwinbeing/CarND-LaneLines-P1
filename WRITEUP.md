# **Finding Lane Lines on the Road** 
***

![alt text][image1]

### Introduction

The project uses a pipeline to detect lane markings and extrapolate full lane lines with the computer vision algorithms.

The algorithms are included:

- Color Selection
- Grayscale, 
- Gaussian Blur
- Canny Edge Detection
- Region of Interest Selection
- Hough Transform Line Detection

[//]: # (Image References)

[image1]: ./resources/preface.png "Preface"
[image2]: ./resources/original_images.png "Original"
[image3]: ./resources/color_selection.png "Color selection"
[image4]: ./resources/gaussian_blur_grayscale.png "Gaussian blur grayscale"
[image5]: ./resources/canny_edge_detection.png "Canny Edge Detection"
[image6]: ./resources/region_of_interest.png "Region of Interest"
[image7]: ./resources/hough_transform.png "Hough Transform"
[image8]: ./resources/average_and_extrapolate.png "Average/extrapolate"
[image9]: ./resources/video.gif "Video"

### Pipeline


The pipeline consisted of the following steps:

- Convert RGB to HLS
- Apply combined mask to select yelow and white colors
- Convert RGB to grayscale
- Apply a slight Gaussian blur
- Perform Canny edge detection
- Define a region of interest and mask away the undesired portions of the image
- Retrieve Hough lines
- Consolidate and extrapolate lines and apply them to the original image

**Original images**

The test images have the shape (540,960,3), meaning a height of y, a width of x, and 3 RGB channels.

![alt text][image2]


**Color selection**

Select those white and yellow lines.

- Use cv2.inRange to filter the white color and the yellow color seperately.
- The function returns 255 when the filter conditon is satisfied. Otherwise, it returns 0.
- Use cv2.bitwise_or to combine these two binary masks.
- The combined mask returns 255 when either white or yellow color is detected.
- Use cv2.bitwise_and to apply the combined mask onto the original RGB image

Tuning the values of lower and upper thresholds until you retain as much of the lane lines as possible while dropping everything else. the output image as below.

![alt text][image3]

**Gaussian blur and grayscale transform**

The smoothing filter used directly affects the results of the Canny algorithm. Smaller filters cause less blurring, and allow detection of small, sharp lines. A larger filter causes more blurring, smearing out the value of a given pixel over a larger area of the image. Larger blurring radii are more useful for detecting larger, smoother edges.


![alt text][image4]


**Edge detection**

There are many ways to detect edges on an image but one of the most popular is the Canny Edge algorithm. The multi-stage algorithm first computes gradient intensity represenations of the the input image, applies thresholding using a lower and upper boundary on gradient values, and then tracks edges using hysteresis (suppressing weak edges that aren't connected to strong edges)

The algorithm will first detect strong edge (strong gradient) pixels above the high threshold, and reject pixels below the low threshold. Next, pixels with values between the low threshold and high threshold will be included as long as they are connected to strong edges. The output edges is a binary image with white pixels tracing out the detected edges and black everywhere else.

![alt text][image5]

**Region of interest**

When finding lane lines, the sky and the hills are not important, will be maksed.
Generally speaking, we are interested in the area surrounded by the trapezoid
 below:

![alt text][image6]

**Hough transform**

We will use the OpenCV function HoughLinesP to find lines in the image.
There are several parameters you'll need to tweak and tune:

- rho: Distance resolution of the accumulator in pixels.
- theta: Angle resolution of the accumulator in radians.
- threshold: Accumulator threshold parameter. Only those lines are returned that get enough votes (> `threshold`).
- minLineLength: Minimum line length. Line segments shorter than that are rejected.
- maxLineGap: Maximum allowed gap between points on the same line to link them.


![alt text][image7]

**Average and extrapolate**

Remove the line with slope is ~0, the horizontal lines are also filtered out by set |slope value|<0.2.

Apply linear regression(OpenCV function polyfit) to get an average line.



![alt text][image8]


### Video
![alt text][image9]




### Potential shortcomings with current pipeline

This pipeline could not work properly under the following condition.

- The camera is moved or rotated.
- On steep, curve road
- Strong light, dark, fog, rain or snow. 


### Suggest possible improvements

- Adjust the parameters dynamically
- Improve stability of lane line

