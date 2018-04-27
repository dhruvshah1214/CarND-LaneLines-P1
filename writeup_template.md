# Finding Lane Lines on the Road - Writeup

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. First, I converted the images to grayscale, and then applied a Gaussian blur, starting with a kernel size of 3. When I got to the challenge video, however, I noticed that some of the lane markers weren't being picked up, so fiddled around with it a bit more. Next was canny edge detection - I used a low and high threshold of 60 and 180, which seemed to be working fine. After that was the region of interest mask, in which I initially used hardcoded pixel values; I realized later this wouldn't be practical for video feed with different sizes, so made all the mask points ratios of the image width and height. The last two steps are the Hough lines and overlaying the lines onto the original image.

For the draw_lines() function, I sorted into left line and right line by slope: positive slope => left line, negative slope => right line. Any line which had an absolute value slope under 0.4 (any super flat line) was filtered out and not considered. Each line also had a "weight" (the length of the line in pixels) that could be used for a weighted average when considering the final two lines. 

For the final two lines, I considered two methods: the **weighted average**, and a **linear regression** (with numpy's `polyfit()` method)
The linear regression was extremely sensitive to any points caught by the canny edge detector that were far away from the line. The weighted average, especially because these "outliers" were short in length, was much less susceptible. 

### 2. Identify potential shortcomings with your current pipeline

This sort of a pipeline is extremely ineffecient and unsafe for lane finding:
* the region of interest mask assumes the lane is in the very center of the image
* different cameras will have different settings/outputs and will require different thresholds on canny detection and different kernel sizes with blurring
* it tries to fit a line to a road which very well could be curved; it should be a quadratic or cubic fit
* in general, I felt like a lot of the parameters were tuned specifically to these three inputs, while a camera running on a self-driving car will see lane lines that haven't been trained for/tuned to before and must still find the lane lines
* on extremely light roads, the camera will not find the lane lines because the edges won't contrast enough


### 3. Suggest possible improvements to your pipeline

Possible improvements...
* run a quadratic or cubic fit on the current image
* use a camera calibration technique
* use distortion correction
* use perspective transforms to try to "rectangularize" the image


### Further Reading
See my [Medium article](https://medium.com/@dhruv_shah/diary-of-a-self-driving-car-engineer-finding-lane-lines-59cae8f7c669) about the process.
Also see this [YouTube video](https://youtu.be/DKz87oYV8bk) of my final result.
