# **Finding Lane Lines on the Road** 

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 7 steps.

1. Convert the image into grayscale.

[image1]: ./test_images_output/gray_img.jpg "Grayscale"

2. Apply Gaussian Blur to the grayscaled image with a kernel size of 5(must be odd) to smooth out edges.

[image2]: ./test_images_output/blur_img.jpg "Gaussian"

3. Apply Canny edge cv2 helper function to the gaussian blurred image with a low threshold of 50 and a high threshold of 150.

[image3]: ./test_images_output/edges_img.jpg "Canny"

4. Find the region of interest pertaining only to the lane currently driven in.  This is done by using the vertices from bottom of the image near the lane lines on both sides and two points near the middle of the image is on the left and right so that only the lane in front is detected.

[image4]: ./test_images_output/ROI_img.jpg "ROI"

5.  Apply Hough transformation to the ROI image to find lane lines on a blank(black) image.  Shorter lines were discarded in order to remove any small outlier marks on the road.  The rest of the parameters were tweaked using trial and error.


6.  Draw the hough lines using the draw_lines function.  In order to draw a single line on the left and right lanes, I modified the draw_lines() function by calculating the slope of each line.  If the slope was positive i placed it in right lane lists, if it was negative i put it in left lane lists because our images coordinate origin is the top left of the image(thus any lines on left travelling upwards will be negative).  During this I also filtered out lines with small slopes as without this I had jitters in testing on yellow lines video and the drawn lines would occassionally jump inside the lane.  Using those lists I used to fit a polynomial of degree 1 to the point lists for the left and right and found the minimum and maximum points(start and endpoints of road lines) based on the region of interest that fit the polynomial.  Using those points cv2.line was used to draw a line through the points.

[image5]: ./test_images_output/hough_img.jpg "hough"

7. Use the weighted_img function to overlay the drawn lane lines from the blank canvas on top of the original image.

[image6]: ./test_images_output/overlay_img.jpg "overlay"

### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when a curve appears in the road, my pipeline only takes into account and draws lane lines for a continuously straight road, if a curve appears the lines would not be able to be drawn properly.  Extrapolating lane lines on a curved road would not have as extreme slopes at all points  and I throw non-extreme slopes out.

Another shortcoming could be that the Region of interest is static and always looking at only the horizon, if in a road there is a sharp turn for a lane the ROI might not capture the full lane ahead which could lead to disaster.

Another shortcoming would be that the pipeline ignores smaller lines(<50px), if a road had a series of smaller lines they would not be detected or draw properly.

Another shortcoming would be that the pipeline assumes all lanes and lane lines are perfectly straight, some lanes may have portions where they become smaller or bigger leading to non-perfectly straight lines.

Another shortcoming would be that the pipeline does not detect lane lines properly when the pavement color turns non-black(like in the challenge video) and thus has a empty list of points.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to add dynamic region of interests.  A static region of interest is fine for the use case of a continuous straight line but to account for curves and turns in lanes/roads a dynamic region of interest will be required to fully visualize the lanes.

Another potential improvement could be to detect not straight lane lines.

Another potential improvement would be to detect lanes when the pavement color is not black and blends in with the lane line colors.



