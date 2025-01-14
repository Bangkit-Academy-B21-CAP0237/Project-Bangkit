# MADANI
> Mapping and Data Assesment for Natural Incident

Madani is an application that makes it easier for volunteers to carry out disaster mitigation by classifying routes and impact buildings based on post-disaster satellite images.

From many disasters that have occurred in Indonesia, the problem that often occurs in the disaster mitigation process is the difficulties of determining the areas and buildings affected by the disaster, and determining the logistics distribution channels needed after a disaster occurs. Manual monitoring can be done, but it will require more resources such as human and financial resources.

Disaster mitigation follow the processes included in the standard operational procedures according to their level of disaster. MADANI is intended to support one of these processes, mainly in estimating areas and buildings affected by disasters, as well as in determining post-disaster logistics distribution channels.


## Mobile Development

### Feature

- Onboarding Screen

![https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Mobile%20Development/Media/Slide2.png](https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Mobile%20Development/Media/Slide2.png)

- Login

![https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Mobile%20Development/Media/Slide2%201.png](https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Mobile%20Development/Media/Slide2%201.png)

- Sign Up

![https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Mobile%20Development/Media/Slide3.png](https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Mobile%20Development/Media/Slide3.png)

- Building Damage Detection Mapping

![https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Mobile%20Development/Media/Slide4.png](https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Mobile%20Development/Media/Slide4.png)

- Road Extraction Mapping

![https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Mobile%20Development/Media/Slide5.png](https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Mobile%20Development/Media/Slide5.png)

- Central Announcement

![https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Mobile%20Development/Media/Slide7.png](https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Mobile%20Development/Media/Slide7.png)

- Information On The Latest Situation In The Post-Disaster Area

![https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Mobile%20Development/Media/Slide8.png](https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Mobile%20Development/Media/Slide8.png)

### Application Flow

![https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Mobile%20Development/Media/Madani.png](https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Mobile%20Development/Media/Madani.png)

### Prototype Application Using Adobe XD

![https://cdn.discordapp.com/attachments/828891691670437890/849286637778108486/unknown.png](https://cdn.discordapp.com/attachments/828891691670437890/849286637778108486/unknown.png)

### Built With

- [Adobe XD](https://www.adobe.com/products/xd.html)
- [Android Studio Native](https://developer.android.com/studio?gclid=Cj0KCQjwh_eFBhDZARIsALHjIKdN_DkjWIonOgxDbdueJpm-h_o4UVbMTdztmVgdcnxHzT1WV4vY3oYaAvcAEALw_wcB&gclsrc=aw.ds)
- [Retrofit 2](https://square.github.io/retrofit/)
- [Kotlin](https://kotlinlang.org)
- [Firebase Storage](https://firebase.google.com/docs/storage)
- [Firebase Firestore](https://firebase.google.com/docs/firestore)
- [REST API MADANI](https://mapping-area.dennyalfa.com/)


## Machine Learning

### Overview

> We create a machine learning model that can classify building damage and detect road in areas affected by natural disasters.


<img src="https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Machine%20Learning/Media/Overview.jpg" alt="Overview"/>

### Building Damage Classification

#### Dataset
We use xBD dataset, a dataset for assessing building damage from satellite imagery. xBD provides pre and post-event satellite imagery from a variety of disaster events with building polygons, classification labels for damage types, labels of damage level, and correspoinding satellite medatada. Detailed information can be found at [xView2](https://xview2.org/) site. We use the Palu 2018 tsunami disaster satellite data provided by xBD which can be downloaded from [here](https://www.kaggle.com/auliawicaksono/palu-disaster-satellite-images). This dataset contain 226 pre and post disaster satellite imagery, with 16,764 building polygons.

<img src="https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/af2a97051daa43d2821308da5d4b1fd6cd757e01/Machine%20Learning/Media/Building%20Damage%20Classification%20Dataset.jpg" alt="Building Damage Detection Dataset" width="500"/>

#### Data Preparation

###### Crop Building Image

<img src="https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Machine%20Learning/Media/Building%20Damage%20Detection%20Preprocessing.jpg" alt = "Crop Building Image" width="500"/>

The image that will be used for training is a polygon image that has been extracted from a complete satellite image. After do some extraction process, then we generate a csv file with polygon ID and it's corresponding damage labels. We will use this csv later to input data into the model using [flow_from_dataframe](https://www.tensorflow.org/api_docs/python/tf/keras/preprocessing/image/ImageDataGenerator#flow_from_dataframe).  We will apply multiple augmentations to our training data for all polygon images such as ; horizontal flip, vertical flip, rotation, width and height shift. We resize our input images to 128x128, this number is not too small to cause loss of necessary information, but not too large to cause our CNN performance slowed down.

###### Undersampling

<img src="https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Machine%20Learning/Media/Building%20Damage%20Classification%20Undersampling.jpg" alt = "Undersampling" width="500"/>

There is an imbalance number of classes in the 2018 Palu tsunami dataset. We have explored the model and train the model with [class weights](https://www.tensorflow.org/tutorials/structured_data/imbalanced_data#train_a_model_with_class_weights), but because the imbalance is very extreme (there is only 1 building polygon that is labeled in class 3 out of a total of 16,764 building polygons), so we decide to reduce the classification target from 4 classes to binary classification. We apply undersampling technique to change the composition of the training dataset because the number of buildings that has no damage and the number of destroyed buildings are imbalance.

##### Model

We create a Keras Model with the [Sequential](https://www.tensorflow.org/api_docs/python/tf/keras/Sequential) linear stack of layers. All Convolutional blocks will use ReLU for the activation parameter, We call [Flatten](https://www.tensorflow.org/api_docs/python/tf/keras/layers/Flatten) method to transform 3 dimensional feature maps into 1 dimensional tensor.

<details>
<summary>CNN Architecture</summary>
<img src="https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Machine%20Learning/Media/Building%20Damage%20Classification%20Plot%20Model.png" alt = "Model Plot 00"/>
</details>


<details>
<summary>Model Accuracy</summary>
<img src="https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Machine%20Learning/Media/Building%20Damage%20Classification%20Model%20Accuracy.JPG" alt = "Model Accuracy width="500"/>
</details>

### Road Extraction

#### Dataset
This dataset is provided by Road Extraction Dataset from [DeepGlobe Challenge](https://www.kaggle.com/balraj98/deepglobe-road-extraction-dataset). DeepGlobe Challenge provided us with images that paired with labels. The image itself is a satellite imagery shot of regions that contain road in it. And the label mask is a grayscale image, with white standing for road pixel, and black standing for background.

The images that we used to train our model is satellite imagery shot of regions that contain road in it. And the label mask is a grayscale image, with white standing for road pixel, and black standing for background. This images we used only from the training folder from you can see in the [DeepGlobe Challenge](https://www.kaggle.com/balraj98/deepglobe-road-extraction-dataset) dataset and from approximately 6000 images and labels later split it into validation dataset in training model.

<img src="https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Machine%20Learning/Media/Road%20Extraction%20Dataset.jpg" alt="Road Extraction Training Images" width="500"/>

#### Data Preparation

We create our own data generator for the road extraction model as we can see in the [03 Road Extraction - Data Preparation & Exploration.ipynb](https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/448ca0a910cedcb73557add9a88c1cef04663de7/Machine%20Learning/03%20Road%20Extraction%20-%20Data%20Preparation%20&%20Exploration.ipynb). First, we created two empty numpy arrays for each images and labels using [numpy.zeros](https://www.geeksforgeeks.org/numpy-zeros-python/), then we convert the satellite images and labels into arrays using [OpenCV](https://docs.opencv.org/master/) and append them into the created numpy arrays. This will make each empty numpy arrays filled with images and labels arrays.

##### Image Arrays

<img src="https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/35318c9dde11941b138e53814c745834fcb69ff0/Machine%20Learning/Media/Road%20Extraction%20Images%20Arrays.png" alt="Road Extraction Image Arrays" width="500"/>

##### Labels Arrays

<img src="https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/9cb1e1e3bbc4141e7c1c6068a55a11944836e6d4/Machine%20Learning/Media/Road%20Extraction%20Labels%20Arrays.png" alt="ResUnet Architecture" width="500"/>

We will not use anymore modification (yet) such as augmentation because we need to process the images and the labels as it is to mantain the preciseness.

#### Model

<details>
<summary>ResUnet Architecture</summary>
<img src="https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/d034a6e5c3fb363c73c16db65fb633c3402ae49f/Machine%20Learning/Media/Residual%20UNet%20Architecture.png" alt = "ResUnet Architecture"/>
</details>

<details>
<summary>Model Accuracy</summary>
<img src="https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Machine%20Learning/Media/Road%20Extraction%20Model%20Accuracy.JPG" alt = "Model Accuracy width="500"/>
</details>

# Cloud Computing
Designing architecture for cloud system and build the environment to store the resource and provide server to do the process in google cloud service. Then create a database with MySQL for organizes, integrates, and control the output from the process in server and create Rest Api with Laravel framework to simplifying communication between database and android to serve the data output 

## Service
- Google Storage

Google storage is used to store all resource data either input and output. We create 2 bucket and using the url to call the object from storage.
- AI Platform Notebook

AI Platform Notebook is used as a server for running the machine learning process. It will call the model and doing image identification process with ML program.
- Compute Instances

Compute Instance is used to run MADANI server application. We used two instances for api server and for machine learning program.
- MySql

We used MySql database to organize and control the output from machine learning process.
- Framework Laravel
We used Laravel to build rest API service to connect database and android as a client. It's a comprehensive framework and logically structured.


## Process Production

1. Designing architecture cloud

    - GCP Architecture

        ![https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/Arch.jpg](https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/Arch.jpg)

2. Setup Environment

    - Create Bucket
    
        ![https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/c1.png](https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/c1.png)
    - Create VM Instances for API

        ![https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/c2.jpeg](https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/c2.jpeg)
    - Create Notebook AI
 
        ![https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/c3.jpg](https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/c3.jpg)

3. Create Database
    - DB Eaver
    
        ![https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/c4.jpeg](https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/c4.jpeg)
    - Database Schema
    
        ![https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/c5.jpeg](https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/c5.jpeg)

4. Build Rest API
    - Models
   
        ![https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/c6.jpeg](https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/c6.jpeg)
    - Controller
 
        ![https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/c7.jpeg](https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/c7.jpeg)
    - api.php
    
        ![https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/c8.jpeg](https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/c8.jpeg)
    - POSTMAN
    
        ![https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/c9.jpeg](https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/c9.jpeg)
    - API Server
    
        ![https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/c10.jpeg](https://github.com/Bangkit-Academy-B21-CAP0237/MADANI/blob/master/Cloud%20%20Computing/c10.jpeg)

5. Migrating all resource to cloud

