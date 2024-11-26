## Font Classification using SVM on FFI dataset

### Dataset Introduction

The dataset that I choose is called Font from Image (FFI), which is established by [Francois](https://github.com/frobertpixto/font-from-image/tree/main) in 2018. All the data that contained in FFI are synthetic images with fonts, where there are totally 11,760 images that are classified into 35 categories, with each class contains 336 images. Each class contains with 86 images with the size of 50*50 and 250 images with three letters with the size of (150*50). Simultaneously, the author also contained a font generator based on macOS system, which can automatically generate different images with different fonts.

I simply check some of the basic information of the data through coding, and randomly select 2 images with different sizes, namely, $50 * 50$ and $150 * 150$, which is shown below.

<div align="center">
  <img width="444" alt="截屏2024-11-26 19 17 37" src="https://github.com/user-attachments/assets/90677cc6-ab26-4d06-9d9c-84d4b55f7ffd">
</div>

Meanwhile, I also checked what the fonts of each category looked like, as well as their distribution in pictures and texts. By which I mean I checked the first text of each font category and the plot is shown below.

<div align="center">
<img width="726" alt="截屏2024-11-26 19 18 54" src="https://github.com/user-attachments/assets/1512da10-b494-412d-a815-e4a04387f90e">
</div>

### Data preprocessing

The proposed data preprocessing workflow is shown below, which is divided into 4 steps:

1. Firstly, remove the noise from the image in the dataset, then extract the image from the grayscale image.
2. Then locate the positions where all text sub images appear in the image to extract them.
3. The next step is to divide the image containing three letters, that is, the original image size is 150 * 50 into three equal parts.
4. Finally, resize all the processed images to a size of 40 * 40

<div align="center"><div align="center">
<img width="412" alt="截屏2024-11-26 22 24 15" src="https://github.com/user-attachments/assets/0d3130f6-9f43-453b-b2d4-9bd4f88dd712">
</div>

Although our image is a composite image, in order to prevent the noise inside the image from affecting the final prediction results, and to facilitate the detection of image edges during feature extraction, I decided to firstly use dilate convolution and erose convolution to process the areas where the image edges may generate noise. For the category of 150 * 50 images, I cut them into three equal parts, which can ensure that only one letter remains in each image after pre-processing. Finally, resize all images to 40 * 40 to ensure the same size when saving each image. The below plot shows the image of 50 * 50 and 150 * 50 after preprocessing respectively. 

<div align="center" style="display: flex; justify-content: center; gap: 20px;">
  <img src="https://github.com/user-attachments/assets/00b97d38-f4a5-4ff9-a49e-e87d8cfaabef" alt="左图" width="276">
  <img src="https://github.com/user-attachments/assets/647c5910-7077-40c6-9b9f-88f7082d8e22" alt="右图" width="424">
</div>



