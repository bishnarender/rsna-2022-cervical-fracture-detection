# rsna-2022-cervical-fracture-detection
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

Here, look at image and mask of 4 different patients. All C[1-7] vertebraes are combined in mask.
![train_images_and_their_mask](https://user-images.githubusercontent.com/49610834/221358169-bf5c2665-0a30-4c6c-a36b-f3e659d9d37b.png)


I used 128x128x128 input, to train 'efficientnet v2' model, for segmenting C[1-7] vertebraes (7ch output).

128x128x128 are in the format z*x*y where 'z = slice number', 'x = width of slice' and 'y=height of slice' of an image.

After the training was completed, I predicted 3d masks for each vertebrae for all 2018 samples in the training set.

Here, is an example of predicted masks of C[1-7] vertebrae.

