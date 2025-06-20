# Image‑Colorization Project
Ori Bloch & Amir Grossman
Technion ECE faculty, 46211 deep learning course project, spring 2025

---

## Overview
It this project we implemented and tested 3 methods for colorization of black and white images.
This Repo contains all the final code for the project for your use.
Colour breathes new life into historic or artistic black‑and‑white images.  
In this project we evaluate:

---

## Quick‑look at Approaches & Results
| Method | One‑line idea | Pros | Cons | Example* |
|--------|---------------|------|------|----------|
| **Diffusion + ControlNet** | Noise → denoise; depth locks geometry | Vivid, cinematic | GPU heavy, may hallucinate | `docs/img/diffusion.png` |
| **Plain U‑Net (Opt‑3)** | 4‑down / 4‑up convs with skips | Fast CPU, structure true | Pastel colours | `docs/img/unet.png` |
| **ResNet‑U‑Net + perceptual** | ResNet‑18 encoder + VGG loss | Smoother colour | Slight dimness | `docs/img/resnet.png` |

\*Replace with your screenshots located in **docs/img/**.

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

---

## Usage
```
Each notebook contains a full implementation and documentation, including all needed installs and imports.
For Image_Colorization_UNET you will need to include in your notebook the dataset zip file from the link below (see references).
---

## Theoretical Background

### Diffusion + ControlNet
* **Stable Diffusion v1.5** iteratively removes noise; ControlNet injects depth guidance each step.  
* MiDaS predicts a single‑image depth map that is normalised and duplicated into 3‑channels.  
* Text prompt adds global style; three hyper‑parameters (`strength`, `guidance_scale`, `control_scale`) balance fidelity vs vibrance.  

### U‑Net Colouriser
* Encoder compresses grayscale L channel to a bottleneck; decoder upsamples while skip‑connections preserve edges.  
* Trained with MSE on L → RGB pairs, learns texture‑to‑colour statistics (sky→blue, grass→green).  
* Runs < 1 s on CPU, outputs sharp but slightly muted images.  

---

## Conclusions
* **Diffusion + ControlNet**: richest colours, expensive compute, occasional hallucinations.  
* **U‑Net family**: lightning fast, faithful geometry, softer palette.  
* Best pixel accuracy came from Option‑3 plain U‑Net.  
* Future work: hybrid pipeline—U‑Net lays base colours, diffusion boosts saturation without altering structure.

---

## References
1. <https://www.kaggle.com/datasets/theblackmamba31/landscape-image-colorization?resource=download> - Dataset
2. <https://www.kaggle.com/code/salimhammadi07/pix2pix-image-colorization-with-conditional-wgan> - Dataset usage
3. <https://www.youtube.com/watch?v=qS45HRPq_64> - Using UNET for image colorization
4. <https://arxiv.org/pdf/2204.02980> - Analysis of Different Losses for Deep Learning Image Colorization
5. <https://samgoree.github.io/2021/04/21/colorization_companion.html> - different loss functions for image colorization
6. <https://www.kaggle.com/code/blurredmachine/vggnet-16-architecture-a-complete-guide> - VGG16 architecture
