# **Finding Lane Lines on the Road**


### The goals of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

[//]: # (Image References)

[image1]: ./steps_images/01-original.jpg "original"
[image2]: ./steps_images/02-gray.jpg "gray"
[image3]: ./steps_images/03-clipped_gray.jpg "clipped_gray"
[image4]: ./steps_images/04-U_img.jpg "U_img"
[image5]: ./steps_images/05-inverse_U_img.jpg "inverse_U_img"
[image6]: ./steps_images/06-blured_gray_img.jpg "blured_gray_img"
[image7]: ./steps_images/07-blured_U_img.jpg "blured_U_img"
[image8]: ./steps_images/08-gray_img_edges.jpg "gray_img_edges"
[image9]: ./steps_images/09-U_img_edges.jpg "U_img_edges"
[image10]: ./steps_images/10-combined_edges.jpg "combined_edges"
[image11]: ./steps_images/11-mask_edges.jpg "mask_edges"
[image12]: ./steps_images/12-hogh_lines.jpg "hogh_lines"
[image13]: ./steps_images/13-add_weighted.jpg "add_weighted"

---

### Reflection

#### 1. Pipeline Description
My pipeline consisted of 10 steps:
![alt text][image1]

1- I converted the original photo to the grayscale.

`gray_img=grayscale(image)`
![alt text][image2]

2- I make any graylevel `< 200` in the gray image to be black to get the white color.

`gray_img[gray_img<200]=0
`
![alt text][image3]

3- I converted the original photo to the U channel in YUV color space because the yellow color is dark in this channel and the white and black colors are gray so we can gat the yellow color easily.

`U_img=U_channel(image)
`
![alt text][image4]

4- I invert the U channel to make the yellow color to be the light color.

`U_img=255-U_img
`
![alt text][image5]

5- I used the gaussian blur to reduce the noise in the result images of step 2,4.

`
gray_img=gaussian_blur(gray_img, kernel_size)
`

`
    U_img=gaussian_blur(U_img, kernel_size)`
![alt text][image6]

![alt text][image7]

6- I used canny to get the edges in the result images of step 5.

`edges_gray=canny(gray_img, low_threshold_gray, high_threshold_gray)
`

`edges_U=canny(U_img, low_threshold_U, high_threshold_U)
`
![alt text][image8]

![alt text][image9]

7- I merged the 2 images from step 6 in one image.

`edges= np.zeros_like(edges_gray)`
    `edges[edges_gray==255]=255`
    `edges[edges_U==255]=255`
![alt text][image10]

8- I applied the region of the interest on the image from step 7.

`mask_edges=region_of_interest(edges, vertices)
`
![alt text][image11]

9- I applied the hough transform to the image from step 8.

Then I calculated the slope, the length and the intercept to every line.

Then I filterd the lines to take only (-1<=slope<=-0.4) or (0.4<=slope<=1).

Then I took the weighted average to all the left and the right lines to calculate the average slope and intercept for the right and the left lanes.

for the video:
- the take the average between the current frame and the previous frame calculations to reduce the noise error
- if the current right or the left lane couldn't be found ,this lane will be considered as the previous one.
- if the lane couldn't be found for 10 sequential frames, no lanes will be drawn until it finds a lane.

Then I draw the 2 lanes.

`line_img=hough_lines(mask_edges, rho, theta, threshold, min_line_len, max_line_gap)
`
![alt text][image12]

10- the last step I added the drawn lanes to the original image.

`image=weighted_img(line_img,image, α, β, λ)
`
![alt text][image13]


#### 2. Potential shortcomings the current pipeline


- This code may fail in the night times
- If a car found in the region of interest, the code may fail to detect the lanes
- the current pipline doesn't detect the curvature

#### 3. Possible improvements to current pipeline

- Using the adaptive thresholding to adapt with the day and night times
- Subtact the detected cars from the region of interest
- Use the bird-eye preview to calculate the curvature
