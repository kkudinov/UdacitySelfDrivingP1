

## Project: **Finding Lane Lines on the Road** 


The goals of the project are:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report



[//]: # (Image References)
[image1]: ./test_images_output/Original.png "Original image"
[image2]: ./test_images_output/Grayscale.png "Grayscale and Gaussian smooth"
[image3]: ./test_images_output/Canny_filter.png "Canny filter"
[image4]: ./test_images_output/Canny_with_ROI.png "Canny with ROI"
[image5]: ./test_images_output/Overlay.png "Overlay"

### Desription of the pipeline
1. convert image to the grayscale
2. Apply gaussian smoothing
3. Apply Canny filter for rough line detection
4. Apply a prism-shaped polygon to the Canny image in order to select a region of interest in the image
5. Search for lines in Hough space
6. Sort line fragments by slope (left and right line).
7. Through away noticeble outliers (optional, not implemented)
8. Generate left and right traffic lines coordinates in the image frame by averagind left and right line segments.
9. Draw these lines on the frame.

### Some thoughts on the order of the steps described in the pipeline above:
1. implementation of the Gaussian blur was very helpful for decreasing the noise line fragments
2. Order of steps in the pipeline is important. For example, in the case one first applies a polygon mask, and then canny filter, there always will be an issue with detecting a border of the polygon as an edge due to shart threshold from image to black color. It can be bypassed by removing lines that have same parameters as a polygon boarders. I did not go ths way though, in order not degress too much from the pain task. The benefit of following this approach is  speeding up the computation with custom Canny filter that would ignore area outside teh ROI.
3. The ROI should be defined with respect to the frame boarders. This way if there are videos with different resolution, the code still will work correctly.
4. Step #6 should be detecting the outliers among the detected line segments and disregard them. This would help make lines drown on the video more stable.

### Implementation:
for exact implementation see attached jupiter notebook. 
Full pipeline output for one of the images is below:

original image:
![original image][image1]

Garyscale and gaussian smooth:
![image2]

Canny filter applied:
![image3]

Canny filter applied and ROI selected:
![image4]

Lines overlayed with the original image:
![image5]

### Potential (actuall) issues:
1. Current analysis treats every frame of the video separately from each otehr. While it should not lead to any issues by itself, the drawn lines on the video 'dance' around and jitter. One way to avoid it is to have a feed back between the frames, assuming that the lines do not change much from frame to frame.
2. Sometimes Gaussian smoothing can 'smooth' lines too much, to the nondetectable state. One way to mitigate it is to have adaptive strength of the gaussian smoothing. The gaussian blur radius would be selected based on the overall sharpness of the picture. 
3. Current line smoothing is prone to noise in teh frame. Canny detects additional line segments that can be qaulified as a 'right' or 'left' line (caviats in the road texture. This leads to jitter of the detected line from frame to frame ans sometimes arrors in the line detection. 

### Potential improvements:
Improvements come from the identidied issues above: 
1. Implement a feedback between the frames assuming that traffic line cannot change abruptly between frames. 
2. Implement intellegent gaussian smoothing based on the overall sharpness of the picture
3. During the procesing of the line segments one should implement intellegent averaging of the left and right line segments. That is through away any segments that deviate too much (say, by 2 sigma) from the mean population and only after that average segments. This would allow to get rig of additional jitter due to noise in the frame. 
