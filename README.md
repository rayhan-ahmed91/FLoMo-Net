# FLoMo-Net
Official Implementation of FLoMo-Net (WACV 2026)

<img width="4904" height="5856" alt="FLoMo-Net" src="https://github.com/user-attachments/assets/057720e4-b98d-4cd1-80d2-3c2635e428f7" />


FLoMo-Net: A Novel Task-Adaptive Mixture of Experts Routing Framework with Frequency and Uncertainty Correction for Medical Image Segmentation

Authors: Md Rayhan Ahmed, Patricia Lasserre

## 1. Overview

FLoMo-Net is a modular, U-Net–style architecture designed for efficient and reliable 2D medical image segmentation.  
It follows a structured "route → select → refine → correct" pipeline through four core modules:

- Local–Global Mixture of Experts (LoGo-MoE) for adaptive multi-scale routing  
- Dual-Attention Selective Aggregator (DASA) for frequency-guided channel and spatial selection  
- Frequency-Aware Multi-Scale Refinement (FAMSR) bridge for texture and boundary enhancement  
- FP/FN Corrective Attention Module (FPFN-CAM) for entropy- and cosine-guided uncertainty correction  

Across four medical imaging modalities, FLoMo-Net achieves state-of-the-art accuracy, faster inference, and fewer parameters compared to 13 competitive baselines.

## 2. Key Features

FLoMo-Net introduces several innovations that significantly enhance segmentation performance:

- Adaptive multi-scale processing through LoGo-MoE
- Frequency-aware refinement using 2D-DCT and spatial saliency pooling in DASA
- Spectral–spatial enhancement in the bottleneck using FAMSR
- Uncertainty-driven correction using entropy and cosine dissimilarity maps via FPFN-CAM
- Three scalable variants (B0, B1, B2) for computational flexibility

These elements collectively improve boundary quality, reduce false positives and false negatives, and support robust generalization across modalities.

## 3. Architecture
3.1 Encoder
The encoder consists of four stages.
Each stage includes:
- LoGo-MoE with 7 experts and a global semantic branch
- Soft routing weights for adaptive expert selection
- DASA applied at stages 3 and 4 for frequency–spatial refinement
- Outputs from the encoder are: E1, E2, E3, E4.

<img width="7272" height="4041" alt="LoGo-MoE" src="https://github.com/user-attachments/assets/47e3ad7d-690c-4670-85c6-80dc60ae9450" />

  
<img width="7008" height="2424" alt="DASA" src="https://github.com/user-attachments/assets/bf1d1623-4535-4286-b1ad-99754d16fc19" />


 3.2 Bridge (FAMSR)
The bottleneck (bridge) enhances the representational quality of the encoded features.
It combines:
- 2D-DCT-based low/high-frequency decomposition
- Dilated convolutions with rates 4, 8, and 12
- SE channel recalibration
- Spatial gating via joint GAP and GMP descriptors

This produces frequency-augmented bridge features B. 


<img width="9308" height="5117" alt="FAMSR" src="https://github.com/user-attachments/assets/d0cb9648-998d-46ff-89d6-03f64caa2fef" />


3.3 Decoder
Each decoder stage performs the following operation:
Di = FPFN_CAM( Ei , DASA( LoGoMoE( Up(Di-1) ) ) )
Components include:
- Upsampling of previous decoder output
- Lightweight LoGo-MoE
- DASA-based refinement
- FPFN-CAM for correcting FP/FN tendencies

This sequential refinement progressively sharpens boundaries and recovers ambiguous regions.

3.4 FP/FN-CAM (Uncertainty Correction)
Given an encoder–decoder feature pair (E, D), FPFN-CAM:
- Projects E and D into a shared latent space
- Cross-gates the projected features
- Computes an entropy dispersion map
- Computes a cosine dissimilarity map
- Fuses these into a unified corrective mask
- Refines the decoder output to reduce FP/FN errors

This module is essential for consistent boundary localization and restoration of subtle structures.

<img width="5100" height="4266" alt="FPFN-CAM" src="https://github.com/user-attachments/assets/28a5e69d-3e17-4c73-a223-4b4d376e7977" />


## 4. Dataset Preparation

FLoMo-Net is evaluated on four public datasets:
- Montgomery County (MC) – chest X-ray
- CVC-ClinicDB – colonoscopy
- BUSI – breast ultrasound
- ACDC – cardiac MRI (converted from 3D to 2D slices)

Preprocessing
- Resize images to 224 × 224
- Normalize pixel values to [0, 1]
- Extract relevant slices for ACDC

Recommended Augmentations
- Horizontal and vertical flipping
- Rotation, scaling, cropping
- CLAHE
- Gaussian noise and blur
- Elastic transformations
- Random shadow and brightness jitter

## 5. Training
Training Configuration
- Optimizer: Nadam
- Initial learning rate: 0.0004
- LR decay: ×0.1 after 10 stagnant epochs
- Batch size: 16
- Epochs: 100
- Early stopping: patience 15
- Evaluation: 5-fold cross-validation

Loss Function
A hybrid loss combining:
- Focal Tversky Loss
- BCE + Dice Loss
This balances overlap accuracy with suppression of difficult FP/ FN regions.

## 6. Results
Quantitative Performance

Across all datasets, FLoMo-NetB2 achieves:
- CVC-ClinicDB: 96.02 mDSC / 92.16 mIoU
- BUSI: 93.96 mDSC / 88.92 mIoU
- MC: 98.31 mDSC / 96.70 mIoU
- ACDC: 92.70 mDSC / 85.68 mIoU

These results show consistent improvements in accuracy, boundary quality, and robustness.

Qualitative Results

FLoMo-Net produces:
- Sharper and more stable boundaries
- Improved small lesion recovery
- Reduced false positives
- Cleaner segmentation masks

<img width="2122" height="1384" alt="output6" src="https://github.com/user-attachments/assets/0478d453-8469-4c16-9028-17a97b7f6b9e" />


If you use FLoMo-Net in your research, please cite:

@inproceedings{Ahmed2026FLoMoNet,
  title={FLoMo-Net: A Novel Task-Adaptive Mixture of Experts Routing Framework with Frequency and Uncertainty Correction for Medical Image Segmentation},
  booktitle={IEEE/CVF Winter Conference on Applications of Computer Vision (WACV)},
  year={2026},
  author={Ahmed, Md Rayhan and Lasserre, Patricia}
}
