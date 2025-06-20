# Image-Colourisation Project

## Table of Contents
1. [Background](#background)
2. [Quick‑look at Approaches & Results](#quick-look)
3. [Repository Hierarchy](#repository-hierarchy)
4. [File Usage](#file-usage)
5. [Conclusions](#conclusions)
6. [References](#references)

---

<a name="background"></a>
## 1 Background
Restoring colour to monochrome photographs revives lost context.  
We compare two deep‑learning families:

* **Diffusion + ControlNet** – large generative model guided by depth and text.
* **CNN / U‑Net variants** – light encoder–decoders that directly predict colour.

Goal: weigh realism, vibrance, speed and compute.

---

<a name="quick-look"></a>
## 2 Quick‑look at Approaches & Results

| Method | Idea in one line | Pros | Cons | Example* |
|--------|-----------------|------|------|----------|
| **Diffusion + ControlNet** | Noise → denoise while depth locks geometry | Vivid, cinematic | GPU‑heavy, may hallucinate | `docs/img/diffusion.png` |
| **Plain U‑Net (Opt‑3)** | 4‑down / 4‑up convs with skips | Fast CPU, structure true | Pastel colours | `docs/img/unet.png` |
| **ResNet‑U‑Net + perceptual loss** | ResNet‑18 encoder, VGG loss | Smoother colour | Slight dimness | `docs/img/resnet.png` |

\*Replace with real screenshots.

---

<a name="repository-hierarchy"></a>
## 3 Repository Hierarchy
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

<a name="file-usage"></a>
## 4 File Usage

| Script / Notebook | Run command | Output |
|-------------------|-------------|--------|
| `notebooks/Diffusion_Based_V2.ipynb` | Colab GPU → *Runtime > Run all* | Coloured PNG in `outputs/` |
| `src/unet.py` | `python train_utils.py --model unet` | `checkpoints/unet_best.pth` |
| Inference | `python infer.py --model-path MODEL --img gray.jpg` | `colorized_gray.jpg` |

**Setup**

```bash
pip install -r requirements.txt
python download_weights.py   # SD + ControlNet checkpoints
```

---

<a name="conclusions"></a>
## 5 Conclusions
* **Diffusion + ControlNet**: rich colours, slower, can invent detail.  
* **U‑Net family**: fast, faithful, softer palette.  
* Best pixel accuracy: fine‑tuned plain U‑Net.  
* Next step – hybrid: U‑Net base + light diffusion to boost vibrance.

---

<a name="references"></a>
## 6 References
1. R. Zhang *et al.* “Colorful Image Colorization,” ECCV 2016  
2. Intel‑ISL MiDaS depth <https://github.com/intel-isl/MiDaS>  
3. ControlNet repo <https://github.com/lllyasviel/ControlNet>  
4. Stable Diffusion v1.5 weights (RunwayML)
