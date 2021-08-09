---
layout: post
title:  " Project#1 Face Mask Dectection using Machine Learning and Deep Learning"
author: baskar
image: assets/images/mask_image.jpeg
categories: [ML & DL projects]
tags: [Python, ML, SVM]
featured: True
hidden: True
applause_button:       true
---


Construct a CNN model to detect if a person is wearing a face mask or not with your webcam or mobile camera.


I build a very simple webapp using pretrained tensorflow,Keras Facenet modeland Support vector machine classifier detect face mask in an uploaded image.

For building this model, I will be using the face mask dataset provided by Prajna Bhandary. It consists of about 1,376 images with 690 images containing people with face masks and 686 images containing people without face masks.

### Step 1:Data preprocessing and training the SVM classifier

The total number of images in our dataset in both categories. We can see that there are 690 images in the ‘yes’ class and 686 images in the ‘no’ class.


```python
The number of images with facemask labelled 'yes': 690 
The number of images with facemask labelled 'no' : 686
```

i.In each train image , the face is detected and embedding vector of faces generated using the pre trained Keras Facenet model

ii.The generated faces array is passed as input to the support vector machine classifer and classified as classes category 'mask' or 'no-mask' along with confidence internal 

The below code snippet describes the steps i,detecting the faces in a train image and saving the output to a numpy array/ 
The output is saved as numpy array.


```python
# loads the pre-trained keras FACENET model
model=FaceNet()

# Load the train images
df_train = pd.read_csv('/d/project2/Input/train.csv')
filenames_train=df_train[df_train.columns[0]]
labels_train=df_train[df_train.columns[1]]

embed_list_train=[]
label_train_list=[]
k=0
for i in tqdm.tqdm(filenames_train):
    # extract the embedding vector of faces from an image using FACENET model
    faces_array_train = model.extract(i, threshold=0.95)
    for j in range(len(faces_array_train)):
        face_embed_dict=faces_array_train[j]
        face_embed=face_embed_dict['embedding']
        embed_list_train.append(face_embed)
        label_train_list.append(labels_train[k])
    k=k+1

embed_list_train=np.array(embed_list_train)
label_train_list=np.array(label_train_list)

#save the output to numpy array
np.savez('svm_processed_data.npz', embed_list_train, label_train_list)
```

The below code snippet describes step ii, using the extract faces embedding vector and its corresponding labels SVM model is trained and saved.


```python
# load dataset generated in step i
data = load('/d/project2/src/svm_processed_data.npz')
trainX, trainy, testX, testy = data['arr_0'], data['arr_1'], data['arr_2'], data['arr_3']
print('Dataset: train=%d, test=%d' % (trainX.shape[0], testX.shape[0]))
```


```python
# fit model
model = SVC(kernel = 'rbf',gamma='scale', probability=True)
model.fit(trainX, trainy)
# predict
yhat_train = model.predict(trainX)
yhat_test = model.predict(testX)
# score
score_train = accuracy_score(trainy, yhat_train)
score_test = accuracy_score(testy, yhat_test)
# summarize
print('Accuracy: train=%.3f, test=%.3f' % (score_train*100, score_test*100))
```
Accuracy: train=99.074, test=91.367

```python
# save the model to disk
filename = 'svm_keral_classifier_final_model.sav'
pickle.dump(model, open(filename, 'wb'))
```

### Step 2: Creating a stremlit App to detect face image in a uploaded image using the trained SVM classifier and  pre-trained Keras FACENET model

The below code is a stremlit web app,when the user uploads the image,the mask or no-mask classified image is displayed 


```python
import streamlit as st
from PIL import Image, ImageEnhance
import numpy as np
import cv2
from keras_facenet import FaceNet
import os
from tensorflow.keras.applications.mobilenet_v2 import preprocess_input
from tensorflow.keras.preprocessing.image import img_to_array
from tensorflow.keras.models import load_model
import pickle

# Setting custom Page Title and Icon with changed layout and sidebar state
st.set_page_config(page_title='Face Mask Detector', page_icon='😷',
                   layout='centered', initial_sidebar_state='expanded')


#The below function takes the input uploaded image using FACENET model and the trained SVM classifier, generates the label 'mask' or 'no-mask' 
def mask_image():
    global RGB_img
    #load our serialized face detector model from disk
    #load facenet models
    print("[INFO] loading face detector model...")
    face_detect_model=FaceNet()

    #load mask classifier models
    print("[INFO] loading face mask detector model...")
    
    with open('./face_mask_classifier_app/svm_keral_classifier_final_model.sav', 'rb') as f:
      model = pickle.load(f)

    embed_list_test=[]
    face_box_list=[]
    face_confidence_list=[]

    # load the input image from disk
    image = "./face_mask_classifier_app/images/out.jpg"
    img=cv2.imread(image)
    faces_array_train = face_detect_model.extract(image, threshold=0.95)
    for j in range(len(faces_array_train)):
        face_embed_dict=faces_array_train[j]
        face_embed=face_embed_dict['embedding']
        face_confidence=face_embed_dict['confidence']
        face_box=face_embed_dict['box']
        embed_list_test.append(face_embed)
        face_box_list.append(face_box)
        face_confidence_list.append(face_confidence)

    face=np.array(embed_list_test)
    #for i in range(len(face)):
    label_pred = model.predict(face)
    proba = model.predict_proba(face)
    if label_pred == 0:
        label = "Mask"
    else:
        label= "No Mask"
    color = (0, 255, 0) if label == "Mask" else (0, 0, 255)
    X=face_box_list[0][0]
    Y=face_box_list[0][1]
    w=face_box_list[0][2]
    h=face_box_list[0][3]
    (X, Y) = max(0, X), max(0, Y)
    endX =  X + w
    endY=  Y + h
    #include the probability in the label
    label = "{}: {:.2f}%".format(label, proba[0][0] * 100)
    cv2.putText(img, label, (X, Y - 10),
    cv2.FONT_HERSHEY_SIMPLEX, 0.45, color, 2)
    cv2.rectangle(img, (X, Y), (endX, endY), color, 2)
    RGB_img = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)

mask_image()


def mask_detection():
    #local_css("css/styles.css")
    st.markdown('<h1 align="center">😷 Face Mask Detection</h1>',
                unsafe_allow_html=True)
    activities = ["Image"]
    st.set_option('deprecation.showfileUploaderEncoding', False)
    st.sidebar.markdown("# Mask Detection on?")
    choice = st.sidebar.selectbox(
        "Choose among the given options:", activities)

    if choice == 'Image':
        st.markdown('<h2 align="center">Detection on Image</h2>',
                    unsafe_allow_html=True)
        st.markdown("### Upload your image here ⬇")
        image_file = st.file_uploader("", type=['jpg'])  # upload image
        if image_file is not None:
            our_image = Image.open(image_file)  # making compatible to PIL
            im = our_image.save('out.jpg')
            saved_image = st.image(
                image_file, caption='', use_column_width=True)
            st.markdown(
                '<h3 align="center">Image uploaded successfully!</h3>', unsafe_allow_html=True)
            if st.button('Process'):
                st.image(RGB_img, use_column_width=True)

    
mask_detection()
```

### Step 3: Face mask detector streamlit app demo

Checkout the created face mask detector stream lit app https://share.streamlit.io/baskar-t/face_mask_classifier_app/main/face_mask_classifier_app/app.py.

Please check out the github repository https://github.com/Baskar-t/face_mask_classifier_app

