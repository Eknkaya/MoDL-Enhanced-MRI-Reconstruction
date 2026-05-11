# Enhanced MoDL for MRI Reconstruction

This repository contains the enhanced implementation of the **Model-Based Deep Learning (MoDL)** architecture for solving inverse problems in accelerated MRI reconstruction. 

Building upon the original MoDL framework, this project introduces significant architectural modifications to the optimization pipeline to improve structural fidelity and prevent L2-induced blurring.

## 🚀 Key Improvements & Features

1. **Multi-Objective Composite Loss Function:**
   Replaced the standard Mean Squared Error (MSE) with a custom objective function combining $L_2$ norm, $L_1$ norm (for sparsity and sharp edges), and SSIM (for perceptual quality).
   `Loss = 0.5 * L2 + 0.1 * L1 + 0.4 * SSIM`

2. **Advanced Optimization Pipeline:**
   * **Exponential Decay LR Scheduler:** Integrated into the Adam optimizer to prevent learning rate starvation and overshooting (Decay rate: 10% per epoch).
   * **Gradient Clipping:** Constrained gradients within a `[-1.0, 1.0]` threshold to prevent the exploding gradient problem inherent in unrolled network architectures.

## 📁 Repository Structure
* `trn.py`: Main training script containing the composite loss and LR scheduler graph.
* `tstDemo.py`: Testing script for generating reconstructions and calculating PSNR/SSIM.
* `model.py`: Core unrolled MoDL architecture (CNN Regularizer + DC Block).

## Preliminary Results
The models were evaluated using the Peak Signal-to-Noise Ratio (PSNR) over a 10-epoch constraint to analyze learning dynamics:

| Model Configuration | Epochs | PSNR (dB) |
| :--- | :---: | :---: |
| Baseline (Original Paper) | 50 | 39.07 |
| **Our Composite Loss (Constant LR)** | 10 | **34.34** |
| **Our Composite Loss + LR Scheduler** | 10 | **33.59** |

*Note: The hyperparameter analysis revealed that a 10% decay per epoch causes "learning rate starvation" in short training runs. Decay steps are currently being optimized (e.g., decay every 5 epochs) for the final 50-epoch full-scale training.*

## How to Use

**1. Clone the repository:**
```bash
git clone https://github.com/Eknkaya/MoDL-Enhanced-MRI-Reconstruction.git
cd MoDL-Enhanced-MRI-Reconstruction