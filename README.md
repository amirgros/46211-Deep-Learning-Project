# Image‑Colorization Project
Ori Bloch & Amir Grossman
Technion ECE faculty, 46211 deep learning course project, spring 2025

![image](https://github.com/user-attachments/assets/71dcd723-3ca1-40b3-9d4a-b7bbda7ce4fe)

---

## Overview
It this project we implemented and tested 3 methods for colorization of black and white images.
This Repo contains all the final code of the project, for documentation and public use.

---

## Quick‑look at Approaches & Results
| Method | One‑line idea | Pros | Cons | Example |
|--------|---------------|------|------|----------|
| **Diffusion + ControlNet** | Noise → denoise; depth locks geometry | Vivid, cinematic | GPU heavy, may hallucinate | <img src="https://github.com/user-attachments/assets/a29335d6-d41d-400d-a0eb-c661bc7cfb34" width="96" height="96"> <img src="https://github.com/user-attachments/assets/26045e5c-9028-450b-b458-1d1ca0ca3210" width="96" height="96"> |
| **Plain U‑Net (Opt‑3)** | 4‑down / 4‑up convs with skips | Fast CPU, structure true | Pastel colours | ![image](https://github.com/user-attachments/assets/e1a777e9-eb7c-4c1d-8661-097fa1c00ebb) |
| **ResNet‑U‑Net + perceptual** | ResNet‑18 encoder + VGG loss | Smoother colour | Slight dimness | ![image](https://github.com/user-attachments/assets/f79d0c64-ca94-45c8-946f-49836fb079d1) |

---

## Repository Hierarchy
```
.
├── Diffusion based/
│   └── Diffusion_Based_V2.ipynb
├── CNN based/
│   ├── Image_Colorization_UNET.ipynb
│   └── Colorization_ResNet_UNet_conn_Perceptual.ipynb
└── README.md
```

## Usage

Each notebook contains a full implementation and documentation, including all needed installs and imports.
For Image_Colorization_UNET you will need to include in your notebook the dataset zip file from the link below (see references).


## Theoretical Background

### Diffusion + ControlNet
* **Stable Diffusion v1.5** iteratively removes noise; ControlNet injects depth guidance each step.  
* MiDaS predicts a single‑image depth map that is normalised and duplicated into 3‑channels.  
* Text prompt adds global style; three hyper‑parameters (`strength`, `guidance_scale`, `control_scale`) balance fidelity vs vibrance.  

### U‑Net Colorizer
* Encoder compresses grayscale L channel to a bottleneck; decoder upsamples while skip‑connections preserve edges.  
* Trained with MSE on L → RGB pairs, learns texture‑to‑colour statistics (sky→blue, grass→green).  
* Runs < 1 s on CPU, outputs sharp but slightly muted images.  

### U‑Net With Pre-Trained Encoder
* Encoder based on pre-trained Resnet18.
* Only training final decoder layers and refinment layers to reduce epoch time and improve generalization.
* Trained with Perceptual Loss + L1.
* Enhanced using HSV Boost.
* Outputs uniformly colored images, but somewhat dim and with better results for smaller images (best seen for 128x128).  

![image](https://github.com/user-attachments/assets/7cbc335f-1b56-4cd0-8c8d-06792e9ab018)

---

## Conclusions
* **Diffusion + ControlNet**: richest colours, expensive compute, occasional hallucinations.  
* **U‑Net family (CNN based)**: lightning fast, faithful geometry, softer palette.  
* Best result came from Option‑3 of plain U‑Net.
* Future work: hybrid pipeline—U‑Net lays base colours, diffusion boosts saturation without altering structure.

---

## References
1. <https://www.kaggle.com/datasets/theblackmamba31/landscape-image-colorization?resource=download> - Dataset
2. <https://www.kaggle.com/code/salimhammadi07/pix2pix-image-colorization-with-conditional-wgan> - Dataset usage
3. <https://www.youtube.com/watch?v=qS45HRPq_64> - Using UNET for image colorization
4. <https://arxiv.org/pdf/2204.02980> - Analysis of Different Losses for Deep Learning Image Colorization
5. <https://samgoree.github.io/2021/04/21/colorization_companion.html> - different loss functions for image colorization
6. <https://www.kaggle.com/code/blurredmachine/vggnet-16-architecture-a-complete-guide> - VGG16 architecture
