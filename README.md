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
