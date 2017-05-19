## Vehicle Detection Project

[//]: # (Image References)
[image2]: ./output_images/hog.jpg
[image1]: ./output_images/hist.jpg
[image3]: ./output_images/windows.jpg
[image4]: ./output_images/heat.jpg
[video1]: ./project_video.mp4

I started the project from reading the provided vehicle and non-vehicle datasets. Based on that I computed the best parameters for my features.

#### Extraction of the features from the training images.

The code for this step is contained in the seventh code cell of the [IPython notebook](./project_video_result.mp4).

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using multiple color spaces and HOG parameters of `orientations=12`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:

![alt text][image2]

From the picture above the HLS space has shown good feature set especially in L and S channels, so it was decided to use it for the experiments with the pictures. Moreover, pixels per cell parameter was increased to 16, which was decreasing amount of extra features. HOG was extracted from all the channels available.

Afterwards, I tried to extract histogram features (code cell 5) again in different color spaces. The example with 256 bins is shown below.

![alt text][image1]

However, later experiments have shown that it is better to reduce amount of the bins to 32 to reduce size and overall quality of the features. A HLS color space was chosen for those features as well.

Raw pixel features have shown themselves feasible as well (code cell 8).

Whole feature extraction procedure is shown in the code cell 9.

#### Classifier training

To choose a right one, three classifier models were considered (SVM with different kernels, decision trees and naive bayes). The process of choice is shown in the code cells 12-15. SVM with rbf kernel has shown itself as the best, however it was overfitting in the real dataset, so the Linear SVC was considered.

To train a classifier, the dataset was divided into training and validation sets manually to fix the problems with the sequences in the datasets. This resulted in lower accuracy score, but in better fit in the actual picture and videos.

The features were extracted and then scaled to prevent dominance of some parameters. A scaler was saved for further usage during testing.
The dataset creation is described in the 10th code cell, and the training of the final classifier is shown in the cell number 16.

### Sliding Window Search

I have chosen 4 rows in 4 different scales along the lower part of the image, as the cars are "mostly" located there. Here is an example of this grid. The scales and position were chosen corresponding to the natural position of cars on a road and the image. The overlapping was set to 0.75, as it resulted in better quality than smaller ones. Below the image with the grid is shown.

![alt text][image3]

### Test images and video

Each window was resized to 64x64 and the features were extracted using the same method described before. Afterwards, they were scaled using the scaler used for a dataset. The boxes predicted with SVM were feed to form a heatmap based on overlapping which was thresholded to avoid false positives. I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected. The same workflow was used to identify the vehicles on the video.

![alt text][image4]

Here's a [link to my video result](./project_video_result.mp4)

In the video additional false positives filter was implemented. It was counting previously predicted boxes for the heatmap as well. This allowed to increase the threshold and filter some more false boundaries. The code for the workflow is located in two last cells of the notebook.

---

### Further improvements and problems

Despite the pipeline generally was able to find the cars and track them along the video, there are many false positives on the opposite lines (maybe cars as well are triggering the classifier). Moreover, sometimes the tracker is losing the car when it goes far from the windows defined and can trigger on shadows (mostly filtered). The possible improvements could be introduction more smart tracking of the vehicle in the sliding windows as well as more solid classifier to distinguish the details.
