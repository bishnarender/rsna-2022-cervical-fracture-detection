# rsna-2022-cervical-fracture-detection
## 20th place score achieved.
![rsna_submission](https://user-images.githubusercontent.com/49610834/232320186-691792cf-b4ee-4824-a9e9-22407436176a.png)


-----

### Start 
For better understanding of project, read the files in the following order:
1. eda.ipynb 
2. train_1_efficient.ipynb
3. slicing_author.ipynb
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

128x128x128 are in the format z,x,y respectively. where 'z = slice number', 'x = height of slice' and 'y = width of slice' of an image.

After the training was completed, this model became capable of predicting mask for each vertebrae for all 2018 samples in the training set.

Model architecture (showing resnet18 encoder block - efficientnet have similar with different features count).
![train_1_diagram](https://user-images.githubusercontent.com/49610834/221358709-19470e68-d316-430a-b966-11de867b1927.jpg)

-----

### slicing_author.ipynb
In this file, i picked those 75 slices (out of all slices of an image) who have mask predicted, and resized them to 224x224. Thus, we have image in the form 75x224x224. Then, i appended the mask after every 5th image i.e., 5x224x224 + 1x224x224 (mask) = 6x224x224. The appended mask is of center (3rd) image. Here, is how it looks. Center image is the same which was used for mask prediction.
![slicing](https://user-images.githubusercontent.com/49610834/221360768-d5440179-6e1b-4f83-937c-7a2faa5d4200.png)

Thus, our final shape (after mask fixing) become 90x224x224 that is rearranged to 15x6x224x224.



-----

### train_2.ipynb

The output we get in previous step is 15x6x224x224, for a single mask (single vertebrae) for an image 90x224x224. We have 7 such output for 7 vertebrae, for an single image. Thus, for 2018 images we have 2k x 7 = 14k samples for feeding to our second model. 

This model is often called 2.5D CNN because each 2D slice in a vertebrae sample has the information of several adjacent slices, so it is called 2.5D CNN. But the model is a normal 2D CNN.
![train_2_diagram](https://user-images.githubusercontent.com/49610834/221361642-c8ede1a3-5cad-410d-84bb-feacd25ad30c.jpg)

-----

### train_3.ipynb

<b>InstanceNorm was used in place of BatchNorm due to GPU size restriction.</b>

The model structure above being able to train over 'single vertebrae for fracture', does not able to train the patient as a whole for the presence of a all vertebrae fracture. So I picked another model. I arranged all 7 vertebrae (7 times 15x6x224x224) in a single input 105x6x224x224. It treats a patient as one training sample to learn patient all labels.

Model architecture.
![train_3_diagram](https://user-images.githubusercontent.com/49610834/221361931-21286688-7cee-45ff-aebb-05a8a970d283.jpg)

-----

### inference.ipynb
Final inference is ensemble of 5 folds of both models.
1. 5 folds of model presented in train_2.ipynb.
2. 5 folds of model presented in train_3.ipynb.

-----

### Future scope
1. use higher input resolution to train models.
2. use bigger backbones.
3. ensemble more models.
