# Face Mask Detection and Gender Prediction

The datasets which I am using are in this drive link - https://drive.google.com/drive/folders/1H75Z9RLkOxahyk8-4lN2HYE-a4_hSy8s?usp=sharing. 


The face_only_images dataset contains headshot photos of people in masks categorised as Male or Female.

The face-mask-detection-final dataset contains headshot photos of people categorised as WithMask or WithoutMask

The gender_prediction dataset contains headshot photos of people categorised as Male or Female


The first and the third datasets are subsets of the kaggle dataset: https://www.kaggle.com/datasets/tapakah68/medical-masks-part1/code?datasetId=1409004&sortBy=dateRun&tab=profile&excludeNonAccessedDatasources=false


The second dataset is a subset of 

## Approach 
### Pre-Processing
The original kaggle data set for the third and first dataset was huge, about 80GB consisting of 40k images. I only used about 3000 images (evenly distributed into the two classes) to train my model. Furthermore the images were selfies of people and not zoomed in cropped photos of only their faces. 

I had two options, I could use PIL (Python Imaging Library) or MTCNN (Multi-task Cascaded Convolutional Networks) for face detection as these are well known for their results. I ran these for the first 10 images of the data set and realised that MTCNN had much better accuracy in finding faces. PIL couldn't detect faces in some images but MTCNN did.

So I used the MTCNN (Multi-task Cascaded Convolutional Networks) to detect the face in the image and crop it out. A major difficulty which I faced while applying MTCNN on the dataset was that the images of the dataset were large as well about 2000*1000 pixels. This required 10 hours to convert each image to its cropped version. 
Since most CNN image classification models take their image input as of the order 300 * 300, I decided to resize each image to 600 * 600 before applying the MTCNN.

For the second dataset, luckily I found a dataset which already contained the cropped face and small sized verison of the image so I only had to reduce the size of the dataset.
Then all the images were split into training, test and validation sub-folders.
Here, I am reducing the size of the dataset since even at about 3000 images, (2000 train, 500 test and 500 validation) the models which I had trained from scratch took about 150 minutes each to train.

### Model Training and Testing 
I had intended to create gender prediction on masked headhots using the cv2 library. For that, I planned I would need three models -
1) To detect face mask
If Face mask is detected,
2) To predict gender on a image with a person wearing a face-mask
If no face mask
3) To predict gender on a image with a person not wearing a face mask

Even though the second model could theoretically work for the no mask case, I wanted a higher accuracy and hence trained a seperate model for it.

I used EfficientNetV2S as my base model and trained its weights from scratch on each particular task. I also added certain layers at the end so that the model can be used for binary classification using the sigmoid function. After training each model I saved it in the EfficientNetV2S models folder.

I then tested my model on the test data and plotted the confustion matrix to visulaise the results.

![image](https://github.com/vedh18/Task-3/assets/147409775/87360cbf-01f8-465b-8265-6eed85b4cbe1)



### Final Implementation
To implement these models on any image and predict its gender
