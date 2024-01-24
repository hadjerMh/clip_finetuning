<!-- Improved compatibility of back to top link: See: https://github.com/othneildrew/Best-README-Template/pull/73 -->
<a name="readme-top"></a>
<!--
*** Thanks for checking out the Best-README-Template. If you have a suggestion
*** that would make this better, please fork the repo and create a pull request
*** or simply open an issue with the tag "enhancement".
*** Don't forget to give the project a star!
*** Thanks again! Now go create something AMAZING! :D
-->



<!-- PROJECT SHIELDS -->
<!--
*** I'm using markdown "reference style" links for readability.
*** Reference links are enclosed in brackets [ ] instead of parentheses ( ).
*** See the bottom of this document for the declaration of the reference variables
*** for contributors-url, forks-url, etc. This is an optional, concise syntax you may use.
*** https://www.markdownguide.org/basic-syntax/#reference-style-links
-->




<!-- PROJECT LOGO -->
<br />
<div align="center">

  <h3 align="center">Content-based image retrieval (CBIR) with CLIP</h3>
  
</div>

<!-- TABLE OF CONTENTS -->
# Table of Contents

- [Project Overview](#project-overview)
  - [Approaches](#approaches)
  - [Data Details](#data-details)
- [Built With](#built-with)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
- [Usage](#usage)
  - [statistic_study.ipynb](#statistic_studyipynb)
  - [image_similarity_clip.ipynb](#image_similarity_clipipynb)
- [Training and Results](#training-and-results)
  - [Training](#training)
  - [Results](#results)
  - [Analysis of the Results](#analysis-of-the-results)
- [Contributing](#contributing)
- [Contact](#contact)


<!-- ABOUT THE PROJECT -->
# Project Overview

This project is based on Content-based image retrieval (CBIR), in order to identifying near-duplicate images within a historical dataset. 
The final goal is to analyze how information has been disseminated across various newspapers and reviews.

## Approaches

We adopted an approach based on CLIP, utilizing a ViT32 base, and explored three different strategies:

1. **Zero-shot**: Images are encoded to extract features, and their similarity is measured using cosine similarity. Forming a similarity matrix.
2. **Fine-tuning without data augmentation**: The model is fine-tuned, and similarity is measured similarly to the zero-shot approach.
3. **Fine-tuning with data augmentation**: The model undergoes fine-tuning with data augmentation, followed by similarity measurement as in the zero-shot approach.

## Data Details

### Test Set
- The test set comprises similar images and is imbalanced. The majority of the data consists of image pairs, but some classes may have up to 18 similar images.

### Training Set
- The training set includes images along with their corresponding legends from newspapers and reviews.
- The training set does not group all similar images under the same class. For example, out of 2040 images, there are 1796 distinct labels.




## Built With


* Pytorch
* CLIP
* Googletrans
<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- GETTING STARTED -->
## Getting Started



### Prerequisites


* python 3
  ```sh
  pip install -r requirements.txt
  ```

<!-- USAGE EXAMPLES -->
## Usage

### `statistic_study.ipynb`:
- Provides comprehensive information about both the test set and the training set.
- Generates two Excel files: `donnees_IS_new.xlsx` for the test set and `df_training_translated.xlsx` for the training set.
  
For the test set, it identifies images from `donnees_IS.xlsx` and cross-references them within our dataset. It retains only the images present in our data, copies them to the `test_dataset` folder and creates a new excel `donnees_IS_new.xlsx` with our new classes.

The training set, it iterates through all the Excel files in the subdirectories, normalizes column names, extracts legends and image positions. It then identifies images with those positions and a specific naming pattern in each subdirectory. Once the dataframe is finalized, the notebook copies all the training images into the `training_set` folder.

Additionally, the notebook handles the translation of legends written in French and saves an Excel `training_set/df_training_translated.xlsx`. 
In case of encountering a runtime error, the `translate_to_english` function can be executed on batches or reexecuted multiple times on the same dataframe without reinitializing it.

### `image_similarity_clip.ipynb`:
- Takes a sample of 32 random classes in our test set to measure similarity.
- Performs a zero-shot similarity measure on our sample of test images.
- Trains CLIP on our data with and without data augmentation.

Note: For data augmentation, the notebook utilizes torchvision transforms with the following augmentations:

```python
transforms = v2.Compose([
    v2.ToTensor(),
    v2.RandomResizedCrop(size=(224, 224), antialias=True),
    v2.RandomRotation(degrees=(0, 180)),
    v2.RandomHorizontalFlip(p=0.5),
    v2.RandomChannelPermutation(),
    v2.RandomVerticalFlip(p=0.5),
    v2.ColorJitter(brightness=.5, hue=.3),
    v2.GaussianBlur(kernel_size=(5, 9), sigma=(0.1, 5.)),
    v2.RandomInvert(0.5),
    v2.RandomAutocontrast(0.5),
])
```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- Training and Results -->
## Training and Results

### Training
For both trainings, we used the following hyperparameters:
- Learning rate: 10^-6 
- Optimizer: Adam 
- Loss function: Cross-entropy 
- Batch size: 128 
- Patience: 20 

On CLIP with a base of ViT32, the results are shown in the figures below:

Without fine-tuning, convergence after 19 epochs:

![CLIP finetuning without augmentation](assets/clip_finetuning_without_augmentation.png?raw=true)

With fine-tuning, convergence after 37 epochs:

![CLIP finetuning with augmentation](assets/clip_finetuning_with_augmentation.png?raw=true)

### Results
For the results, the metrics of Accuracy, Precision, Recall, F1_Score, and nDCG were calculated.
Note that the test set being imbalanced, we will focus on Accuracy and the nDCG score.

**nDCG (Normalized Discounted Cumulative Gain):** It is used to evaluate the effectiveness of a recommendation system. It measures the ranking quality of the recommended items by considering both their relevance and position in the ranked list. 

The results of the zero-shot, the fine-tuning of CLIP without and with data augmentation, are shown in the table below:

| Metric          | Zero-shot | Without Augmentation | With Augmentation |
| --------------- | --------- | --------------------- | ------------------ |
| Accuracy        | 19.39%    | 23.47%                | 36.73%             |
| nDCG            | 4.81%     | 4.82%                 | 7.57%              |

### Analysis of the Results
The results are not satisfactory, and this could be due to:

 - The training set containing too much noise, where most similar images don't have the same labels.
 - The images being too similar and all representing scenes of a war.
 - In some classes, the similarity in the test set classes is not obvious to the eye.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONTRIBUTING -->
## Contributing

Contributions are what make the open source community such an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated**.

If you have a suggestion that would make this better, please fork the repo and create a pull request. You can also simply open an issue with the tag "enhancement".
Don't forget to give the project a star! Thanks again!

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

<p align="right">(<a href="#readme-top">back to top</a>)</p>






<!-- CONTACT -->
## Contact

Hadjer MOHABEDDINE - https://github.com/hadjerMh

<p align="right">(<a href="#readme-top">back to top</a>)</p>




<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/othneildrew/Best-README-Template.svg?style=for-the-badge
[contributors-url]: https://github.com/othneildrew/Best-README-Template/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/othneildrew/Best-README-Template.svg?style=for-the-badge
[forks-url]: https://github.com/othneildrew/Best-README-Template/network/members
[stars-shield]: https://img.shields.io/github/stars/othneildrew/Best-README-Template.svg?style=for-the-badge
[stars-url]: https://github.com/othneildrew/Best-README-Template/stargazers
[issues-shield]: https://img.shields.io/github/issues/othneildrew/Best-README-Template.svg?style=for-the-badge
[issues-url]: https://github.com/othneildrew/Best-README-Template/issues
[license-shield]: https://img.shields.io/github/license/othneildrew/Best-README-Template.svg?style=for-the-badge
[license-url]: https://github.com/othneildrew/Best-README-Template/blob/master/LICENSE.txt
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://linkedin.com/in/othneildrew
[product-screenshot]: images/screenshot.png
[Next.js]: https://img.shields.io/badge/next.js-000000?style=for-the-badge&logo=nextdotjs&logoColor=white
[Next-url]: https://nextjs.org/
[React.js]: https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB
[React-url]: https://reactjs.org/
[Vue.js]: https://img.shields.io/badge/Vue.js-35495E?style=for-the-badge&logo=vuedotjs&logoColor=4FC08D
[Vue-url]: https://vuejs.org/
[Angular.io]: https://img.shields.io/badge/Angular-DD0031?style=for-the-badge&logo=angular&logoColor=white
[Angular-url]: https://angular.io/
[Svelte.dev]: https://img.shields.io/badge/Svelte-4A4A55?style=for-the-badge&logo=svelte&logoColor=FF3E00
[Svelte-url]: https://svelte.dev/
[Laravel.com]: https://img.shields.io/badge/Laravel-FF2D20?style=for-the-badge&logo=laravel&logoColor=white
[Laravel-url]: https://laravel.com
[Bootstrap.com]: https://img.shields.io/badge/Bootstrap-563D7C?style=for-the-badge&logo=bootstrap&logoColor=white
[Bootstrap-url]: https://getbootstrap.com
[JQuery.com]: https://img.shields.io/badge/jQuery-0769AD?style=for-the-badge&logo=jquery&logoColor=white
[JQuery-url]: https://jquery.com 