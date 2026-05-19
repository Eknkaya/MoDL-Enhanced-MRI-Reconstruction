Enhanced MoDL for MRI ReconstructionPrepared by Ekin KayaProject DescriptionThis repository contains the enhanced implementation of the Model-Based Deep Learning (MoDL) architecture for solving inverse problems in accelerated MRI reconstruction. Building upon the original MoDL framework, this project introduces significant architectural modifications to the optimization pipeline to improve structural fidelity, preserve high-frequency anatomical textures, and prevent standard L2-induced blurring.Original Paper and RepositoryThis project is an extension and improvement of the original MoDL framework:Original Paper: MoDL: Model-Based Deep Learning Architecture for Inverse Problems by H. K. Aggarwal et al. (IEEE TMI 2018).Original GitHub Repository: hkaggarwal/modlOur Modifications & ImprovementsMulti-Objective Composite Loss Function:Replaced the standard Mean Squared Error (MSE) with a custom objective function combining L2 norm, L1 norm (for sparsity and sharp edges), and SSIM (for perceptual quality).Loss = 0.5 * L2 + 0.1 * L1 + 0.4 * SSIMAdvanced Optimization Pipeline:Exponential Decay LR Scheduler: Integrated into the Adam optimizer to prevent learning rate starvation and overshooting.Gradient Clipping: Constrained gradients within a [-1.0, 1.0] threshold to prevent the exploding gradient problem inherent in unrolled network architectures.Requirements and InstallationTo run this project locally, ensure you have Python 3.8+ installed.Clone the repository:git clone [https://github.com/Eknkaya/MoDL-Enhanced-MRI-Reconstruction.git](https://github.com/Eknkaya/MoDL-Enhanced-MRI-Reconstruction.git)
cd MoDL-Enhanced-MRI-Reconstruction
Install the required dependencies:pip install -r requirements.txt
Dataset Download and PreparationThis project utilizes a subset of the NYU fastMRI Brain Dataset (T2-weighted multi-channel scans).Download the fastMRI Brain dataset from the official fastMRI portal.Extract the .h5 files into a local directory (e.g., ./data/fastmri/).The dataset contains fully sampled k-space data. The artificial degradation (Cartesian 1D undersampling mask with 4x acceleration factor and 4% ACS) is applied dynamically within our data loader during runtime.How to Run the Code1. Reproducing Original Baseline Results: To run the standard MoDL architecture using only the L2 (MSE) loss:python trn.py --mode baseline --epochs 50
2. Running Our Improved Version: To train the enhanced model with our Composite Loss and LR Scheduler:python trn.py --mode composite --epochs 10
To evaluate the trained model and generate reconstructions:python tstDemo.py
Comparison of ResultsThe models were evaluated using the Peak Signal-to-Noise Ratio (PSNR) over a constrained 10-epoch window to analyze learning dynamics against the fully converged baseline:Model ConfigurationLoss FunctionEpochsPSNR (dB)Baseline (Original Paper)L2 (MSE)5039.28Our Method (Constant LR)Composite1034.34Our Method (LR Scheduler)Composite1033.59Note: Short training constraint (10 epochs) limits raw PSNR comparison, causing learning rate starvation in the decaying schedule. However, qualitative assessments show our Composite Loss preserves significantly sharper anatomical edges than the L2 baseline.File Structuretrn.py: Main training script containing the composite loss formulation and the modified optimization graph.tstDemo.py: Testing and evaluation script for generating final reconstructions and calculating PSNR/SSIM metrics.model.py: Core unrolled MoDL architecture defining the CNN Regularizer and the Conjugate Gradient Data Consistency (DC) Block.requirements.txt: List of required Python dependencies.Expected Outputs and RuntimeRuntime: Training the improved composite model for 10 epochs takes approximately 5-6 hours on a standard Intel Iris Xe local GPU setup.Expected Output: Upon completion of tstDemo.py, the script will output the average PSNR metrics to the console and save the reconstructed MR images (.png format) to the ./results/ directory, allowing for direct visual comparison against the zero-filled inputs and the ground truth.
# Enhanced MoDL for MRI Reconstruction

* Prepared by Ekin Kaya

## Project Description
This repository contains the enhanced implementation of the **Model-Based Deep Learning (MoDL)** architecture for solving inverse problems in accelerated MRI reconstruction. Building upon the original MoDL framework, this project introduces significant architectural modifications to the optimization pipeline to improve structural fidelity, preserve high-frequency anatomical textures, and prevent standard L2-induced blurring.

## Original Paper and Repository
This project is an extension and improvement of the original MoDL framework:
* **Original Paper:** [MoDL: Model-Based Deep Learning Architecture for Inverse Problems](https://arxiv.org/abs/1712.02862) by H. K. Aggarwal et al. (IEEE TMI 2018).
* **Original GitHub Repository:** [hkaggarwal/modl](https://github.com/hkaggarwal/modl)

## Our Modifications & Improvements
1. **Multi-Objective Composite Loss Function:**
   Replaced the standard Mean Squared Error (MSE) with a custom objective function combining L_2 norm, L_1 norm (for sparsity and sharp edges), and SSIM (for perceptual quality).
   Loss = 0.5 * L2 + 0.1 * L1 + 0.4 * SSIM
2. **Advanced Optimization Pipeline:**
   * **Exponential Decay LR Scheduler:** Integrated into the Adam optimizer to prevent learning rate starvation and overshooting.
   * **Gradient Clipping:** Constrained gradients within a `[-1.0, 1.0]` threshold to prevent the exploding gradient problem inherent in unrolled network architectures.

## Requirements and Installation
To run this project locally, ensure you have Python 3.8+ installed. 

1. Clone the repository:
git clone [https://github.com/Eknkaya/MoDL-Enhanced-MRI-Reconstruction.git](https://github.com/Eknkaya/MoDL-Enhanced-MRI-Reconstruction.git)
cd MoDL-Enhanced-MRI-Reconstruction
2. Install the required dependencies:
pip install -r requirements.txt
## Dataset Download and Preparation
This project utilizes a subset of the NYU fastMRI Brain Dataset (T2-weighted multi-channel scans).
1.Download the fastMRI Brain dataset from the official fastMRI portal.
2.Extract the .h5 files into a local directory (e.g., ./data/fastmri/).
3.The dataset contains fully sampled k-space data. The artificial degradation (Cartesian 1D undersampling mask with 4x acceleration factor and 4% ACS) is applied dynamically within our data loader during runtime.
## How to Run the Code
1. Reproducing Original Baseline Results: To run the standard MoDL architecture using only the L_2 (MSE) loss:
python trn.py --mode baseline --epochs 50
2. Running Our Improved Version: To train the enhanced model with our Composite Loss and LR Scheduler:
python trn.py --mode composite --epochs 10
To evaluate the trained model and generate reconstructions:
python tstDemo.py

## Comparison of Results
The models were evaluated using the Peak Signal-to-Noise Ratio (PSNR) over a constrained 10-epoch window to analyze learning dynamics against the fully converged baseline:
  Model Configuration        Loss Function   Epochs  PSNR (dB)
Baseline (Original Paper)      L2 (MSE)        50     39.28
Our Method (Constant LR)      Composite        10     34.34
Our Method (LR Scheduler)     Composite        10     33.59

Note: Short training constraint (10 epochs) limits raw PSNR comparison, causing learning rate starvation in the decaying schedule. However, qualitative assessments show our Composite Loss preserves significantly sharper anatomical edges than the L2 baseline.

## File Structure
trn.py: Main training script containing the composite loss formulation and the modified optimization graph.
tstDemo.py: Testing and evaluation script for generating final reconstructions and calculating PSNR/SSIM metrics.
model.py: Core unrolled MoDL architecture defining the CNN Regularizer and the Conjugate Gradient Data Consistency (DC) Block.
requirements.txt: List of required Python dependencies.

## Expected Outputs and Runtime
Runtime: Training the improved composite model for 10 epochs takes approximately 5-6 hours on a standard Intel Iris Xe local GPU setup.

Expected Output: Upon completion of tstDemo.py, the script will output the average PSNR metrics to the console and save the reconstructed MR images (.png format) to the ./results/ directory, allowing for direct visual comparison against the zero-filled inputs and the ground truth.
