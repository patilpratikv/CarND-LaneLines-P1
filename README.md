# **Finding Lane Lines on the Road**

<img src="examples/laneLines_thirdPass.jpg" width="480" alt="Combined Image" />

Overview
---

When we drive, we use our eyes to decide where to go.  The lines on the road that show us where the lanes are act as our constant reference for where to steer the vehicle.  Naturally, one of the first things we would like to do in developing a self-driving car is to automatically detect lane lines using an algorithm.

In this project you will detect lane lines in images using Python and OpenCV.

Proposed Solution
---
* Start with an image or frame from a video clip

<img src="test_images/solidWhiteCurve.jpg" width="480" alt="Combined Image" />

* Convert the image to grayscale

<img src="write_up/gray.jpg" width="480" alt="Combined Image" />

* Apply Gaussian smoothing

<img src="write_up/blur.jpg" width="480" alt="Combined Image" />

* Apply Canny edge detection function

<img src="write_up/edges.jpg" width="480" alt="Combined Image" />

* Create mask on image to define working region

<img src="write_up/masked.jpg" width="480" alt="Combined Image" />

* Apply Hough transform on the selected region

<img src="write_up/hough.jpg" width="480" alt="Combined Image" />

* Manipulate the lines to get just two lane lines

<img src="write_up/lines.jpg" width="480" alt="Combined Image" />

* Draw lines on top of original image


<img src="test_images_output/solidWhiteCurve.jpg" width="480" alt="Combined Image" />

Most of the steps above are straight forward and can be easily achieved with OpenCV.

I decided not to use color masking on my first attempt and it worked pretty well for the first part of the project.

The quadrilateral used to mask the image was hardcoded based on the images we received as an input. Ideally we want our function to work on any video stream, so that wasn't a good start. I will come back to this later on, when discussing the Challenge.

The other tricky part was on how to deal with all the lines coming from the Hough transform. For this part I decided to combine all the lines in a Pandas dataframe and split them based on angle thresholds that were fine tuned to isolate the lines that were interesting to us.

After splitting the data into left and right I fitted a line through each of these sets and got an equation for each proposed lane line. Now it was just a matter of finding 2 points for each line and plotting.

Outcome
---

The proposed solution worked well for the first set of problems that we had to solve, but as expected it didn't work well in images that were significantly different than the ones we optimized the function to work on.

Needless to say that the approach also failed miserably when I used it on the challenge video stream.

I was particularly bothered by the fact that we were relying on predetermined mask coordinates and other hardcoded parameters such as the Canny Thresholds.

Overcoming obstacles
---

For the second part of this project I decided that i was going to go for a solution that generalizes better.

I replaced the mask with a series of constraints on how far the Hough lines are expected to be from the middle of the car hood, and introduced a new constraint on the expected angles of these lines.

I also had to  deal with more challenging light and contrast in the image, so color masking could no longer be ignored.

To solve that problem I converted the images from RGB into HSV to allow a better distinction between the colors in several light conditions.

The color masking combined with distance and angle constraints for the hough lines were enough to identify lines of interest and I could then drop the polygonal mask.

The last problem I faced was that it was still not calculating lines for all the frames and the lines could be significantly different from one frame to another depending on how visible the lane line was.

Since we expect this solution to work on public roads it is reasonable to assume that the lane line from the previous frame is a good approximation to where the lane line should be in the current frame. So for all frames that had no lines identified I used the line from the previous frame.

To solve the "shakiness" of the lines I added a smoothing parameter and would only allow lines to change by 15% per iteration on the final version.

The results can be seen in this [video](https://github.com/bguisard/CarND-LaneLines-P1/tree/master/test_videos_output/challenge.mp4)
