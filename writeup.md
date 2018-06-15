# Vehicle Detection Project

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/car.jpg
[image2]: ./output_images/no_car.jpg
[image3]: ./output_images/car_hog.jpg
[image4]: ./output_images/hog.jpg
[image5]: ./output_images/test1.jpg
[image6]: ./output_images/heatmap_test1.jpg
[image7]: ./output_images/test2.jpg
[image8]: ./output_images/heatmap_test2.jpg
[image9]: ./output_images/test3.jpg
[image10]: ./output_images/heatmap_test3.jpg
[image11]: ./output_images/test4.jpg
[image12]: ./output_images/heatmap_test4.jpg
[image13]: ./output_images/test5.jpg
[image14]: ./output_images/heatmap_test5.jpg
[image15]: ./output_images/test6.jpg
[image16]: ./output_images/heatmap_test6.jpg


[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

Vehicle            |  Non-Vehicle
:-----------------:|:-------------------------:
![][image1]        |  ![][image2] 




Then I implemented the hog function from the sklearn library. The code for this step is contained in the get_hog_features of the IPython notebook. 

I then explored different color spaces and different parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`). 

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:

Vehicle            |  HOG
:-----------------:|:-------------------------:
![][image3]        |  ![][image4]


#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and I finally used 9 orientations to keep both the recognition accuracy and reasonable feature vector length and `YCrCb` color space. 

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I tried to trai the linear SVM using different 3 types of features: HOG, Color Histogram and Spatial binned features. My resulting feature vector includes a scaled HOG and color histogram features. For the training were used pictures with 64x64 resolution. The testing accuracy reached 97.5 % on the testing set. 

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I implemented the search using the find_cars function in IPython Notebook. It searches through area of interest (ystart,ystop, whole image width) in the image. It uses search of scales 1-2.2 of (64,64) of original training images.    


#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on 8 scales using YCrCb 3-channel HOG features plus histograms of color in the feature vector, which provided a nice result. I got also much better results on recognicition of distant vehicles using L1-Normalisation instead of using L2-Hys.(although in theory it sould be exactly the other way around.(http://lear.inrialpes.fr/people/triggs/pubs/Dalal-cvpr05.pdf)

### Here are six frames with resulting bounding boxes and their corresponding heatmaps:

Image with Bound Boxes |  Thresholded Heatmap
:---------------------:|:-------------------------:
![][image5]            |  ![][image6]
![][image7]            |  ![][image8]
![][image9]            |  ![][image10]
![][image11]           |  ![][image12]
![][image13]           |  ![][image14]
![][image15]           |  ![][image16]

---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded with 3 detections that map to identify vehicle positions. Then I introduced the Vehicled_detected class with labeled_detections variable and add_detection method.

The add_detection method contains the  thresholded heat map of the last 20 frames. The resulting heatmap is a sum of the last 20 frames and thresholded with history_threshold = 25. 

On resulting heat map I used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The pipeline still has issues in recognition of distant vehicles on one side and false positive on the other side. If I had more time to optimize the performance of the pipeline I would introduce a Vehicle Class with more properties. Each blob found using label function would be check on its size depending on its position on the image. If plausible the new Object of the class would be constructed.  Each Object would be append to the list of objects and tracked.( I would define the plausible max position change per frame... In real implementation a use of fusion of detections of another sensor (e.g. radar), would significantly improve the accuracy of the vehicle detection.   

