# Image‑Colourisation Project

Comprehensive comparison of two AI approaches for adding colour to monochrome photographs.

---

## Overview
Colour breathes new life into historic or artistic black‑and‑white images.  
In this project we evaluate:

* **Diffusion + ControlNet** – state‑of‑the‑art generative pipeline guided by depth and text.  
* **CNN / U‑Net variants** – lightweight auto‑encoders that predict colour directly.

We measure quality, speed and compute cost, and discuss which tool fits which use‑case.

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
├── notebooks/
│   ├── Diffusion_Based_V2.ipynb
│   ├── Colorization_ResNet_UNet.ipynb
│   └── UNet_baseline.ipynb
├── src/
│   ├── unet.py
│   ├── resnet_unet.py
│   ├── dataset.py
│   └── train_utils.py
├── models/
│   └── colorization_release_v2.pth
├── docs/img/
└── README.md
```

---

## Environment & Setup
```bash
git clone https://github.com/your‑org/colourisation.git
cd colourisation
conda env create -f environment.yml      # or pip install -r requirements.txt
python download_weights.py               # downloads SD & ControlNet checkpoints
```
CPU‑only users can skip diffusion notebooks and work with U‑Net scripts.

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
1. <https://www.youtube.com/watch?v=qS45HRPq_64> - Using UNET for image colorization
2. <https://www.kaggle.com/datasets/theblackmamba31/landscape-image-colorization?resource=download> - Dataset
3. <https://arxiv.org/pdf/2204.02980> - Analysis of Different Losses for Deep Learning Image Colorization
4. <https://samgoree.github.io/2021/04/21/colorization_companion.html> - different loss functions for image colorization
5. <https://www.kaggle.com/code/blurredmachine/vggnet-16-architecture-a-complete-guide> - VGG16 architecture
