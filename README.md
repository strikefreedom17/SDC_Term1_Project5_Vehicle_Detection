## Read Me
---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)

[image1]: ./Pics/Example_Pics.png
[image2]: ./Pics/Cars.png
[image3]: ./Pics/Not_Cars.png
[image4]: ./Pics/Processed_Pics.png

[video1]: ./project_video_output.mp4



### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The HOG features extraction is described in "Step 2: Feature Extraction" section in "Main_Training.ipynb". Note that, all of related functions that use in this project are described in "lesson_funtions.py".

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

My features extraction include histrogram feature, spatial bin feature, and hog feature. After several trials and errors, my parameters are listed as follow:

colorspace = 'YCrCb'

use_spatial = 1

spatial_size = (32,32)

use_hist = 1

hist_bins = 32

use_hog = 1

orient = 9

pix_per_cell = 8

cell_per_block = 2

hog_channel = "ALL" # Can be 0, 1, 2, or "ALL"

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried several combination starting from colorspace i.e. RGB vs YUV vs YCrCb. For each colorspace selection I picked, I calculate the accuracy of model fitting and determine which colorspace can yield the best accuracy result. Likewise for the tuning of hist_bins, spatial_size, hist_bins, orient, pix_per_cell, cell_per_block, and hog_channel, I applied the same methodology. Here is the HOG features using RGB and YCrCb colorspace given the car and noncar image:

Car
![alt text][image2]

Not Car
![alt text][image3]

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using "svc.fit(X_train, y_train)". The test data is splitted with 20% of total data size. X stands for features which described above, including spatial bin, histrogram, and hog features. After training, the accuracy validated from test data set is 98.85%. And the SVC prediction given 10 images are all correct. Then, the model is saved as model1.pkl.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I modify the "find_cars" code introduced in the lecture. My window size is 64x64 with ystart = 390, ystop = 640, scale = 1.5, and cells_per_step = 2. These parameters are properly tuned. The scale tuning is very important since it controls the resized of new window. Tuning the scale to be greater than 1 tends to give more robust final vehicle tracking result.


#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

The final processed images are shown below. I did applied sliding window search, then smooth the multiple windows by applying the heatmap. 

![alt text][image4]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
My project output video is shown in "project_video_output.mp4"


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I found that applying the heatmap to smooth out the false detection is not enough. I did apply the average of 5 heatmap frames with threshold =  0.3. The details of smoothing the frames are described in "video_pipeline2(image)" function. However there is still few frames that are false positives.

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I spent a lots of time on training the model to get the acceptable accuracy and tuning the heat map + sliding window parameters + averaging frame parameter as well. So far, there is still some small amount of false positivies. Applying some useful technique such as kalman filter, particle filter to smooth out the false positive should be something useful if it's introduced in the lesson. 
