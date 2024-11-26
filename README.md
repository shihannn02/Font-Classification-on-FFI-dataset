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

Although some pictures do not completely separate the letters, such as the Allura regular class, because they have hyphenation, as shown below.

<div align="center">
<img width="528" alt="截屏2024-11-26 22 28 55" src="https://github.com/user-attachments/assets/6a43fbf6-6fb1-4f5e-b076-88745f05a425">
</div>

After the above operations, I finally retrieve 29260 pictures and saved them under the name 'font_new'folder, and they are saved as .png format. To visualize the final outcome of image preprocessing, I randomly selected 50 images of a category called GreatVibes Regular for viewing below.

<div align="center">
<img width="670" alt="截屏2024-11-26 22 30 47" src="https://github.com/user-attachments/assets/b616166a-cd60-45ae-836a-ca5b0eaa129c">
</div>

### Feature selection and feature extraction 

To improve model accuracy for classification tasks, I explored two approaches for processing features: PCA (Principal Component Analysis) for feature extraction and Random Forest for feature selection. These methods help reduce the complexity of the data by either mapping features to a lower-dimensional space or dropping less important features based on their rankings.

Both methods were applied to binary images generated using the Canny algorithm to detect the global edges of letters. The goal was to evaluate whether:

**Feature extraction (PCA)** better captures spatial connections by fusing features into a low-dimensional representation, or
**Feature selection (Random Forest)** effectively reduces dimensions by removing unimportant features while preserving key spatial information.

#### Feature extraction method based on global image: Canny method

We used the Canny edge detection algorithm for image preprocessing due to its superior boundary detection accuracy, particularly important for handling noise from incomplete letter segmentation. This could be shown below. After comparing PCA and Random Forest for feature extraction, we chose PCA which improved accuracy by approximately 5%. The final implementation combines Canny edge detection, PCA (reduced to 210 dimensions), and an SVM classifier with RBF kernel.

<div align="center">
<img width="659" alt="截屏2024-11-26 22 40 05" src="https://github.com/user-attachments/assets/8a16da34-ba64-4f1a-a66a-dadae7e4a127">
</div>

#### Feature reduction: Canny + PCA

I initially tested PCA dimensionality reduction from 100 to 1000 dimensions (in intervals of 100) and found the best performance between 200-300 dimensions. After further testing this range with 10-dimension intervals, I determined that 210 dimensions yielded optimal results, which was selected as the final PCA parameter.

<div align="center" style="display: flex; justify-content: center; gap: 20px;">
  <img src="https://github.com/user-attachments/assets/123721e7-ce04-4841-8694-fcef1bf6e040" alt="左图" width="276">
  <img src="https://github.com/user-attachments/assets/9fd43250-2386-4047-b993-2bbd7fd4d3c0" alt="右图" width="424">
</div>

#### Feature reduction: Canny + Random Forest

I evaluated Random Forest as an alternative feature reduction method. The initial Random Forest model achieved 73.37% accuracy and helped identify feature importance. After removing 521 less important features (those with importance < 0.00055), the SVM model achieved 70.86% accuracy. While this slightly reduced accuracy, it improved computational efficiency by 50% through eliminating approximately one-third of the features. Since PCA demonstrated superior accuracy, it was selected for the final model training phase.

<div align="center">
<img width="598" alt="截屏2024-11-26 22 44 58" src="https://github.com/user-attachments/assets/a847c23c-f0e0-4a24-ba5e-25da1131a405">
</div>

### Model selection

#### SVM Introduction

In my experiment, the model I used was SVM. SVM is a model based on statistical theory. SVM is a supervised learning algorithm based on statistical principles, which plays an important role in pattern recognition and can be widely used in many fields, including the problem of processing text classification involved in this experiment. I attempts to solve the font style classification problem through SVM, because SVM can reliably and efficiently complete some classification tasks by finding global optimal solutions. Compared with other algorithms, SVM has a good performance in dealing with small samples, nonlinear and high-dimensional problems. However, correspondingly, although SVM can have a good performance on high-dimensional data, when the feature dimension of the training vector increases, its training time and storage space also increase correspondingly. Therefore, the time and space complexity of processing large-scale data that modeled by SVM will increases linearly as the sample data number increase. At the same time, parameter adjustment is not easy for SVM. The preferred parameter adjustment method is resampling, but the side effect is that it requires the computer to do large quantities of computing, which is a very costly and time-consuming choice.

#### Parameter selection

The following are some parameters of SVM that need to be adjusted in my experiment:

**C**: This refers to the penalty term of the objective function, that is, loss function. C can balance margin and samples with wrong classification.

**Kernel**: refers to the method of mapping low dimensional data to high dimensional data. Kernel methods usually include linear, rbf and poly. 

**Gamma**: Only rbf, poly and sigmoid have Gamma values. Among them, the larger the gamma is, the higher the mapping dimension is, and the training result will be correspondingly improved. However, it is easy to cause over fitting, so we should carefully adjust it.

### Experienment results analysis and discussion

I conducted parameter tuning using cross-validation for different SVM kernels:

**Linear kernel**: Best performance at c=0.01, achieving 56.95% accuracy
**RBF kernel**: Best performance at c=1 and gamma=0.01
**Polynomial kernel**: Best performance with degree=2, c=0.01, and gamma=1

Cross-validation indicated that the polynomial kernel with c=0.001 and gamma=1 provided optimal performance on the validation set.

Feature extraction plays an important role in traditional machine learning. One of the focuses of this article is to observe the dimension reduction methods based on feature extraction (PCA) and feature selection (random forest) respectively and see their impact on the final test result of SVM classifier. My experimental results are shown below.

<p align="center">

| Method | Accuracy | Time |
|--------|----------|------|
| Canny | 72.33% | 12m41s |
| Canny + PCA (n_components=210) | 77.19% | - |
| Canny + Random Forest | 70.86% | 6m43s |
  
</p>

When we only do Canny feature extraction on the data, the accuracy rate can reach 72.33%. Combined with PCA dimension reduction method, the accuracy rate is improved by 4.86%. Although the accuracy of the random forest has decreased by 1.47% compared with the Canny algorithm alone, we can find that nearly 1/3 of the dimensions have been lost by observing the time variable, but the time has decreased almost by half. Finally, based on Canny + PCA, I reduced the dimension to 210. I trained three different kernels and observed their relative maximum accuracy based on different kernels.

<p align="center">

| Kernel | Accuracy | Parameter |
|--------|-----------|-----------|
| Linear | 56.95% | C=0.1 |
| Rbf | 75.57% | C=0.1, gamma=0.01 |
| Poly | 80.74% | C=0.01, gamma=1, poly=2 |
  
</p>

#### Analysis of feature selection and extraction

In this experiment, I compared two feature reduction methods based on pixel-level features: PCA and Random Forest. PCA reduces dimensionality by combining existing features, which not only maintains essential information and reduces computational complexity but also preserves spatial relationships between pixels. In contrast, Random Forest directly discards less important features, which means the discarded pixel information and their spatial relationships are permanently lost. This fundamental difference explains why PCA demonstrated better performance in our experiment.

#### Analysis of the time parameter

Due to SVM's computational intensity, parameter tuning was limited in scope. While we achieved 82.70% accuracy with polynomial kernel (c=0.001, gamma=1, degree=2), we couldn't exhaustively test all parameter combinations. The RBF kernel might potentially perform better with finer parameter tuning. Each cross-validation run (k=4) took 5-9 minutes depending on the kernel type, making extensive parameter searching computationally expensive in terms of both time and memory resources. This represents a practical trade-off between optimization potential and computational constraints.

#### Analysis of the predicted results

It can be seen from the above conclusions that the recognition rate of the font of CarterOne is of the worst among all categories, with only 63% accuracy. It can be seen from the confusion matrix that it will be mistaken for a font called FredokaOne-Regular. Based on this observation, I printed 50 pictures of money in these two fonts for viewing, and the results are shown in the following figure. The image on the left is of the CarterOne category, and the image on the right is of the FredokaOne Regular category.

<div align="center">
<img width="726" alt="截屏2024-11-26 22 53 21" src="https://github.com/user-attachments/assets/ab832e82-846f-4992-814b-e70a6a0db803">
</div>

Rather than relying solely on edge detection (which may struggle with visually similar fonts), we could enhance feature extraction by incorporating spatial measurements.

**Note**: All the experienments are done on a personal laptop with MacOS 12.2.1 of CPU M1 2020 and RAM 16GB. The VSCode version is 1.71.1 (Universal).
