# rsna-2022-cervical-fracture-detection
## 30th place score achieved.
![my_submission](https://user-images.githubusercontent.com/49610834/221353934-20d43467-4874-426b-ba17-79597f800f43.png)

-----

### Start 
For better understanding of project, read the files in the following order:
1. eda.ipynb 
2. train_1_efficient.ipynb
3. slicing_org.ipynb
4. train_2.ipynb
5. train_3.ipynb
6. inference.ipynb

For submission to kaggle, i used the file 'inference_for_kaggle.ipynb'.

-----

### train_1_efficient.ipynb
For 3D semantic segmentation, only 87 samples are available in the dataset, but it's sufficient to train 3D semantic segmentation models with good performance.

Here, look at 'image and mask' of 4 different patients. All C[1-7] vertebraes are combined in mask.
![train_images_and_their_mask](https://user-images.githubusercontent.com/49610834/221358169-bf5c2665-0a30-4c6c-a36b-f3e659d9d37b.png)


I used 128x128x128 input, to train 'efficientnet v2' model, for segmenting C[1-7] vertebraes (7ch output). 128x128x128 is repeated 3 times to match model input 3x128x128x128.

128x128x128 are in the format z,x,y respectively. where 'z = slice number', 'x = width of slice' and 'y=height of slice' of an image.

After the training was completed, this model became capable of predicting mask for each vertebrae for all 2018 samples in the training set.

Model architecture.
![train_1_diagram](https://user-images.githubusercontent.com/49610834/221358709-19470e68-d316-430a-b966-11de867b1927.jpg)

-----

### slicing_org.ipynb
In this file, i picked those 75 slices (out of all slices of an image) who have mask predicted, and resized them to 224x224. Thus, we have image in the form 75x224x224. Then, i appended the mask after every 5th image i.e., 5x224x224 + 1x224x224 (mask) = 6x224x224. The appended mask is of last (5th) image. Here, is how it looks.
![slicing](https://user-images.githubusercontent.com/49610834/221360768-d5440179-6e1b-4f83-937c-7a2faa5d4200.png)

Thus, our final shape (after mask fixing) become 90x224x224 that is rearranged to 15x6x224x224.

-----

### train_2.ipynb
The output we get in previous step is 15x6x224x224, for a single mask (single vertebrae) for an image 90x224x224. We have 7 such output for 7 vertebrae, for an single image. Thus, for 2018 images we have 2k x 7 = 14k samples for feeding to our second model. 

This model is often called 2.5D CNN because each 2D slice in a vertebrae sample has the information of several adjacent slices, so it is called 2.5D CNN. But the model is a normal 2D CNN.
![train_2_diagram](https://user-images.githubusercontent.com/49610834/221361642-c8ede1a3-5cad-410d-84bb-feacd25ad30c.jpg)

-----

### train_2.ipynb
The model structure above being able to train over 'single vertebrae for fracture', does not able to train the patient as a whole for the presence of a all vertebrae fracture. So I picked another model. I arranged all 7 vertebrae (7 times 15x6x224x224) in a single input 105x6x224x224. It treats a patient as one training sample to learn patient all labels.

Model architecture.
![train_3_diagram](https://user-images.githubusercontent.com/49610834/221361931-21286688-7cee-45ff-aebb-05a8a970d283.jpg)

-----

### inference.ipynb
Final inference is ensemble of 5 folds of both models.
1. 5 folds of model presented in train_2.ipynb.
2. 5 folds of model presented in train_3.ipynb.
