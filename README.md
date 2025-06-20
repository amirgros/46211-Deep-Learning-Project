# Image‑Colourisation Project

A comparative study of two modern approaches to adding colour back to black‑and‑white photographs:

1. **Diffusion + ControlNet** – state‑of‑the‑art generative model guided by a depth map and text prompt.  
2. **Convolutional Auto‑encoders (U‑Net family)** – lightweight, feed‑forward networks that directly predict colour channels.

The repository contains notebooks, reusable PyTorch modules, pre‑trained weights and a CLI for batch inference.

---

## Table of Contents
1. [Background](#background)
2. [Approach 1 – Diffusion + ControlNet](#diffusion)
3. [Approach 2 – U‑Net Colouriser](#unet)
4. [Quick Side‑by‑Side Results](#results)
5. [Repository Hierarchy](#repo-tree)
6. [Environment & Setup](#setup)
7. [Training & Inference](#usage)
8. [Conclusions](#conclusions)
9. [References](#references)

---

<a name="background"></a>
## 1  Background
Colour brings context: skin tone hints at health, sky hue reveals weather, clothes signal era.  
Manual recolouring is slow; algorithmic methods fall into two camps:

* **Generative (Diffusion)** – create colour by stochastic sampling, excellent at vivid palettes.  
* **Deterministic (CNN)** – predict colour pixel‑wise, fast and faithful but sometimes dull.

We implement both, measure quality, speed and resource demand, and discuss when to choose which.

---

<a name="diffusion"></a>
## 2  Approach 1 – Diffusion + ControlNet

| component | role |
|-----------|------|
| **Stable Diffusion v1.5** | latent denoiser; turns noise → image in ≈50 steps |
| **ControlNet‑depth**      | injects per‑pixel conditioning from a depth map |
| **MiDaS small**           | produces 256 × 256 monocular depth from greyscale |
| **Prompt**                | short text (“realistic, high‑quality colour photo”) steers global style |

**Pipeline**

1. Grayscale 512² → latent `z₀` via VAE.  
2. Add noise proportional to `strength` (0‑1).  
3. At each timestep `t`  
   * U‑Net predicts ε.  
   * ControlNet adds residuals based on depth + text embedding.  
   * Scheduler updates latent (classifier‑free guidance weight = `guidance_scale`).  
4. After `num_inference_steps` decode latent → RGB.

**Key knobs**  
`strength`, `guidance_scale`, `controlnet_conditioning_scale`, `num_inference_steps`.

Hardware: NVIDIA T4 / 12 GB yields ~8 s per 512 px image.

---

<a name="unet"></a>
## 3  Approach 2 – U‑Net Colouriser

| layer stack                    | output size |
|--------------------------------|-------------|
| Input (L‑only)                 | 1 × 224 × 224 |
| Conv 64 stride 2               | 64 × 112 × 112 |
| Conv 128 stride 2              | 128 × 56 × 56 |
| Conv 256 stride 2              | 256 × 28 × 28 |
| Conv 512 stride 2 (bottleneck) | 512 × 14 × 14 |
| TransConv 256 + skip           | 256 × 28 × 28 |
| TransConv 128 + skip           | 128 × 56 × 56 |
| TransConv 64 + skip            | 64 × 112 × 112 |
| TransConv 3 (sigmoid)          | 3 × 224 × 224 |

Training details:

* **Dataset** – 30 k ImageNet frames converted to (L, RGB) pairs.  
* **Loss** – pixel MSE.  
* **Optimiser** – Adam 1e‑3, batch 64.  
* **Scheduler** – ReduceLROnPlateau (factor 0.5, patience 3).

Runs in < 1 s per 512 px on CPU; colours slightly pastel but never hallucinate.

---

<a name="results"></a>
## 4  Quick Side‑by‑Side Results

| ![diff](docs/img/diffusion_demo.png) | ![unet](docs/img/unet_demo.png) |
|-------------------------------------|---------------------------------|
| **Diffusion:** vibrant, cinematic   | **U‑Net:** faithful, softer     |

*(click images in HTML view for full resolution)*

---

<a name="repo-tree"></a>
## 5  Repository Hierarchy
```
.
├── notebooks/                   # Jupyter demo notebooks
├── src/                         # PyTorch modules & training utilities
├── models/                      # pre‑trained checkpoints
├── data/                        # optional small sample dataset
├── docs/
│   └── img/                     # README figures
└── README.md
```

---

<a name="setup"></a>
## 6  Environment & Setup

```bash
git clone https://github.com/your‑org/colourisation.git
cd colourisation
conda env create -f environment.yml   # or pip install -r requirements.txt
python download_weights.py            # SD + ControlNet weights (~5 GB)
```

*CPU‑only users* can skip diffusion notebooks and run U‑Net scripts.

---

<a name="usage"></a>
## 7  Training & Inference

### Train U‑Net from scratch
```bash
python src/train_utils.py --model unet        --data-root data/train --epochs 30 --save-dir checkpoints
```

### Colourise a single image with U‑Net
```bash
python infer.py --model-path checkpoints/unet_best.pth                 --img path/to/grey.jpg --out colour.jpg
```

### Diffusion demo (Colab)
Open **notebooks/Diffusion_Based_V2.ipynb** → *Runtime > Run all*.

---

<a name="conclusions"></a>
## 8  Conclusions
* Diffusion + ControlNet yields rich colour but is GPU‑intensive and can invent content.  
* U‑Net family is lightweight and faithful; colour is muted yet acceptable for archival work.  
* Best pixel accuracy: fine‑tuned plain U‑Net (Option‑3).  
* Future work: hybrid pipeline – U‑Net lays reliable base colours, a light diffusion pass boosts saturation.

---

<a name="references"></a>
## 9  References
1. R. Zhang *et al.* “Colorful Image Colorization,” ECCV 2016.  
2. Intel‑ISL MiDaS depth <https://github.com/intel-isl/MiDaS>  
3. Lvmin Zhang *et al.* “ControlNet” (arXiv 2302.05543).  
4. Stable Diffusion v1.5 weights – RunwayML.  
5. Ronneberger O. *et al.* “U‑Net: Convolutional Networks for Biomedical Image Segmentation,” MICCAI 2015.
