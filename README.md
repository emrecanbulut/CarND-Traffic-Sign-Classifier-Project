# **Traffic Sign Recognition** 

## Writeup

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Build a Traffic Sign Recognition Project**

The goals / steps of this project are the following:
* Load the data set (see below for links to the project data set)
* Explore, summarize and visualize the data set
* Design, train and test a model architecture
* Use the model to make predictions on new images
* Analyze the softmax probabilities of the new images
* Summarize the results with a written report


[//]: # (Image References)

[image1]: ./examples/DataDistribution.png "Data Distribution"
[image2]: ./examples/grayscale.jpg "Grayscaling"
[image3]: ./examples/Cropped.png "Cropped"
[image4]: ./examples/Clockwise10.png "Clock-wise 10 degrees"
[image5]: ./examples/CounterClockwise10.png "Counter clock-wise 10 degrees"
[image6]: ./GTSRB/00010/00000_00015.jpg "No passing for vehicles over 3.5 metric tons"
[image7]: ./GTSRB/00011/00000_00015.jpg "Right-of-way at the next intersection"
[image8]: ./GTSRB/00012/00000_00015.jpg "Priority road"
[image9]: ./GTSRB/00013/00000_00015.jpg "Yield"
[image10]: ./GTSRB/00014/00000_00015.jpg "Stop"
[image11]: ./GTSRB/00040/00000_00015.jpg "Roundabout mandatory"


## Rubric Points
### Here I will consider the [rubric points](https://review.udacity.com/#!/rubrics/481/view) individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one. You can submit your writeup as markdown or pdf. You can use this template as a guide for writing the report. The submission includes the project code.

You're reading it! and here is a link to my [project code](https://github.com/udacity/CarND-Traffic-Sign-Classifier-Project/blob/master/Traffic_Sign_Classifier.ipynb)

### Data Set Summary & Exploration

#### 1. Provide a basic summary of the data set. In the code, the analysis should be done using python, numpy and/or pandas methods rather than hardcoding results manually.

I used the pandas library to calculate summary statistics of the traffic
signs data set. Below statistics can be seen in [the Notebook here.](http://localhost:8888/notebooks/Traffic-Sign-Classifier.ipynb#Step-1:-Dataset-Summary-&-Exploration)

* The size of training set is 34799
* The size of the validation set is 4410
* The size of test set is 12630
* The shape of a traffic sign image is (32,32,3)
* The number of unique classes/labels in the data set is 43

#### 2. Include an exploratory visualization of the dataset.

Here is an exploratory visualization of the data set. It is a bar chart showing how the data is distributed for each class in the training, testing, and validation datasets.

![Data Distribution][image1]

### Design and Test a Model Architecture

#### 1. Describe how you preprocessed the image data. What techniques were chosen and why did you choose these techniques? Consider including images showing the output of each preprocessing technique. Pre-processing refers to techniques such as converting to grayscale, normalization, etc. (OPTIONAL: As described in the "Stand Out Suggestions" part of the rubric, if you generated additional data for training, describe why you decided to generate additional data, how you generated the data, and provide example images of the additional data. Then describe the characteristics of the augmented training set like number of images in the set, number of images for each class, etc.)

As a first step, I decided to convert the images to grayscale because I didn't want the model to perceive the photos belonging of the same traffic sign as if they are different because of the lighting, camera properties, etc.

Here is an example of a traffic sign image before and after grayscaling.

![alt text][image2]

Then, I normalized the data at hand to have ~0 mean and equal variance by using the formula:

`img = (img - 128)/128`

By doing so, the content of the image is not changed, but the optimization will be much easier to proceed numerically.

I decided to generate additional data because after exploration of the data set, I noticed that there were classes with significantly less examples to train on.
Besides, I wanted to be sure that with a little change (like rotation or shifting) on the same image, it wouldn't throw the model completely off.

Therefore, to add more data to the the data set, I added 3 sets of the grayscaled training data with following operations applied on them:

1. Cropped 5 pixel from left, right, up, and down. I did that because after exploration of the data, I was confident most of the images were positioned really good at the center.
And the background of the traffic sign could have affected the model. Here is an example of an original image and an augmented image:

![Cropped sign][image3]

2. Same thing in step 1. But this time, I also rotated the image 10 degrees clock wise. I didn't want to rotate the image too much because first this wouldn't be a real world example that 
I wanted to train the model with (like a speed limit sign rotated 60 degrees). Even if there are examples of that, I assume it should be very rare.
Here is an example of an original image and an augmented image:

![alt text][image4]

3. Same thing in step 1. But this time, I also rotated the image 10 degrees counter clock wise. Here is an example of an original image and an augmented image:

![alt text][image5]


#### 2. Describe what your final model architecture looks like including model type, layers, layer sizes, connectivity, etc.) Consider including a diagram and/or table describing the final model.

My final model consisted of the following layers:

| Layer         		|     Description	        					| 
|:---------------------:|:---------------------------------------------:| 
| Input         		| 32x32x3 RGB image   							| 
| Convolution 5x5     	| 1x1 stride, valid padding, outputs 28x28x6 	|
| RELU					|												|
| Max pooling	      	| 2x2 stride,  outputs 14x14x6  				|
| Convolution 5x5	    | 1x1 stride, valid padding, outputs 10x10x16	|
| RELU					|												|
| Max pooling	      	| 2x2 stride,  outputs 5x5x16	  				|
| Flatten				| Input:5x5x16, Output:400						|
| Fully connected		| Input:400, Output:120							|
| RELU					|												|
| Fully connected		| Input:120, Output:84							|
| RELU					|												|
| Fully connected		| Input:84, Output:43							|
|						|												|
 


#### 3. Describe how you trained your model. The discussion can include the type of optimizer, the batch size, number of epochs and any hyperparameters such as learning rate.

To train the model, I used an AdamOptimizer which tries to come up with the logits giving the minimum error. AdamOptimizer is different than classical stochastic gradient descent. 
It benefits from AdaGrad (Adaptive Gradient Algorithm) and RMSProp (Root Mean Square Propagation). More info on Adam Optimizer can be found [here] (https://machinelearningmastery.com/adam-optimization-algorithm-for-deep-learning/)

I used 10 epochs at first. However, I noticed that there was still room for improvement at the 10th epoch. So, I finally set `EPOCH` to 20.

I kept `BATCH_SIZE` at 128 and `learning_rate` at 0.001 

#### 4. Describe the approach taken for finding a solution and getting the validation set accuracy to be at least 0.93. Include in the discussion the results on the training, validation and test sets and where in the code these were calculated. Your approach may have been an iterative process, in which case, outline the steps you took to get to the final solution and why you chose those steps. Perhaps your solution involved an already well known implementation or architecture. In this case, discuss why you think the architecture is suitable for the current problem.

My final model results were:
* training set accuracy of 0.998
* validation set accuracy of 0.960
* test set accuracy of 0.937

If a well known architecture was chosen:
* What architecture was chosen?

I used LeNet-5 architecture.

* Why did you believe it would be relevant to the traffic sign application?

Traffic signs are nothing but shapes and/or writings. This is what LeNet excels at as Yann Lecun shows with this amazing gif:

![](http://yann.lecun.com/exdb/lenet/gifs/asamples.gif)

So, using with the initial parameters given in the lab, it already resulted in almost 90.5% of accuracy. Then, I thought, if I can get more epoch and augment the data in a way that
would make the model more robust, it would increase the accuracy a bit and I would go from there. To my surprise, it increased the accuracy to 96%. That was a lot more than I had 
expected. My judgment on that was,

1. Either the background behind the signs were affecting the model too much

2. Or the validation set had mostly rotated images

3. Or both.


* How does the final model's accuracy on the training, validation and test set provide evidence that the model is working well?

Accuracy on the training set clearly shows that it is over-fitting. I think that is pretty normal considering the model was taught with those images.
 
Accuracy on the validation and the test set are both over 93% and show that the model is working well. The reason why the validation accuracy is higher than testing accuracy
is because of the dataset size, in my opinion. The model makes more inaccurate predictions on test data set(12630 rows) than the validation set(4410 rows).

### Test a Model on New Images

#### 1. Choose five German traffic signs found on the web and provide them in the report. For each image, discuss what quality or qualities might be difficult to classify.

Here are some of the thirty three German traffic signs that I found on the web:

![alt text][image6] ![alt text][image7] ![alt text][image8] ![alt text][image9] ![alt text][image10] ![Roundabout mandatory][image11]

The last image was the most difficult to classify because if I was to define it to someone who doesn't know the name of the shapes, I would say 'there is a dark area in the middle
and curvy white areas wrapping around it'. If you check label 40, and label 42, they both fit in that definition.

Therefore the model used to predict that 'Roundabout mandatory' sign(40) as the 'End of no passing by vehicles over 3.5 metric tons' sign(42).

When I augmented the training data with rotated and cropped images, the issue was somewhat solved. Although, this image has the lowest confidence level as shown in the softmax probabilities.


#### 2. Discuss the model's predictions on these new traffic signs and compare the results to predicting on the test set. At a minimum, discuss what the predictions were, the accuracy on these new predictions, and compare the accuracy to the accuracy on the test set (OPTIONAL: Discuss the results in more detail as described in the "Stand Out Suggestions" part of the rubric).

Here are the results of the prediction:

| Image			        										|     Prediction	        									| 
|:-------------------------------------------------------------:|:-------------------------------------------------------------:| 
| No passing for vehicles over 3.5 metric tons					|  No passing for vehicles over 3.5 metric tons					|
| Right-of-way at the next intersection							|  Right-of-way at the next intersection						|
| Priority road													|  Priority road												|
| Yield															|  Yield														|
| Stop															|  Stop															|
| No vehicles													|  No vehicles													|
| Vehicles over 3.5 metric tons prohibited						|  Vehicles over 3.5 metric tons prohibited						|
| No entry														|  No entry														|
| General caution												|  General caution												|
| Dangerous curve to the left									|  Dangerous curve to the left									|
| Dangerous curve to the right									|  Dangerous curve to the right									|
| Double curve													|  Double curve													|
| Bumpy road													|  Bumpy road													|
| Slippery road		 				 							|  Slippery road		 				 						|
| Road narrows on the right	   					                |  Road narrows on the right	   					            |
| Road work							                            |  Road work							                        |
| Traffic signals										        |  Traffic signals										        |
| Pedestrians									                |  Pedestrians									                |
| Children crossing						 				        |  Children crossing						 				    |
| Bicycles crossing	 							                |  Bicycles crossing	 							            |
| Beware of ice/snow								            |  Beware of ice/snow								            |
| Wild animals crossing                                         |  Wild animals crossing                                        |
| End of all speed and passing limits					        |  End of all speed and passing limits					        |
| Turn right ahead				 				                |  Turn right ahead				 				                |
| Turn left ahead								                |  Turn left ahead								                |
| Ahead only	                                                |  Ahead only	                                                |
| Go straight or right											|  Go straight or right											|
| Go straight or left	                                        |  Go straight or left	                                        |
| Keep right	                                                |  Keep right	                                                |
| Keep left	                                                    |  Keep left	                                                |
| Roundabout mandatory	                                        |  Roundabout mandatory								            |
| End of no passing                                             |  End of no passing                                            |
| End of no passing by vehicles over 3.5 metric tons	        |  End of no passing by vehicles over 3.5 metric tons	        |


The model was able to correctly guess 33 out of 33 of the traffic signs, which gives an accuracy of 100%.

One reason for that is that there are only 33 images, and compared to the other images, they are relatively less noisy(background, darkness, etc.).

And this was the best accuracy among my other tries, too. It generally yields around 97% with 1 inaccurate prediction that is the 'Roundabout mandatory' sign.

#### 3. Describe how certain the model is when predicting on each of the five new images by looking at the softmax probabilities for each prediction. Provide the top 5 softmax probabilities for each image along with the sign type of each probability. (OPTIONAL: as described in the "Stand Out Suggestions" part of the rubric, visualizations can also be provided such as bar charts)

The code for making predictions on my final model is located in the 19th cell of the Ipython notebook.

As seen in the table below, the model is pretty confident about its predictions with the lowest probability being 96.3%.

| Probability         	|     Prediction	        									| 
|:---------------------:|:-------------------------------------------------------------:| 
| 1.00000000			| Keep right	                                                | 
| 9.99999881e-01		| Keep left	                                                    |
| 9.63293910e-01		| Roundabout mandatory	                                        |
| 1.00000000			| End of no passing                                             |
| 9.99998808e-01	    | End of no passing by vehicles over 3.5 metric tons	        |




