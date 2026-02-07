# BrainMetShare-3-Benchmarking

# Brain Metastasis Segmentation Using Multi-Sequence MRI

## Overview

This project implements a **deep learning–based segmentation model** for automatic detection of **brain metastases** from multi-sequence MRI scans. The work is based on the **BrainMetShare-3 dataset** released by Stanford AIMI and is inspired by the accompanying research paper on brain metastasis detection using deep learning.

Due to hardware and storage constraints, this dataset was selected instead of larger medical imaging datasets such as ChestXpert (≈487 GB). The BrainMetShare-3 dataset (~2 GB) provides high-quality, voxel-level annotations and multiple MRI modalities, making it suitable for model development and evaluation on limited-resource systems.

---

## Dataset

* **Dataset name:** BrainMetShare-3
* **Source:** Stanford Center for Artificial Intelligence in Medicine & Imaging (AIMI)
* **Link:** [https://aimi.stanford.edu/datasets/brain](https://aimi.stanford.edu/datasets/brain)

The dataset consists of multi-sequence brain MRI scans from patients with brain metastases. Ground truth segmentation masks are provided for the training data.

### MRI Modalities Used

Each case contains four MRI sequences, which are used as input channels to the model:

* **T1-weighted pre-contrast (T1-pre):**
  Acquired before contrast injection; provides baseline anatomical structure and tissue contrast.

* **T1-weighted post-contrast (T1-GD):**
  Acquired after gadolinium contrast injection; highlights enhancing tumor regions and abnormal vascularity.

* **BRAVO (Brain Volume Imaging):**
  A high-resolution 3D T1-weighted sequence that provides detailed anatomical information with good signal uniformity.

* **FLAIR (Fluid-Attenuated Inversion Recovery):**
  A T2-based sequence that suppresses cerebrospinal fluid (CSF), improving visibility of lesions near fluid-filled regions.

These four sequences are stacked channel-wise to form a **4-channel input image** for the segmentation model.

---

## Data Organization

```
brainmetshare-3/
├── train/
│   ├── Mets_001/
│   │   ├── t1_pre.nii.gz
│   │   ├── t1_gd.nii.gz
│   │   ├── bravo.nii.gz
│   │   ├── flair.nii.gz
│   │   └── seg.nii.gz
│   └── ...
└── test/
    ├── Mets_201/
    │   ├── t1_pre.nii.gz
    │   ├── t1_gd.nii.gz
    │   ├── bravo.nii.gz
    │   └── flair.nii.gz
    └── ...
```

> Note: Segmentation masks (`seg.nii.gz`) are available only for the training set.
> Test cases with missing MRI modalities were excluded from inference.

---

## Methodology

### Preprocessing

* 3D MRI volumes were decomposed into **2D axial slices**
* Four MRI modalities were stacked as input channels
* All slices were resized to **256 × 256**
* Intensity normalization was applied
* Lesion-containing slices were **oversampled** to handle class imbalance
* Data augmentation (rotations and flips) was used during training

---

### Model Architecture

A **2D U-Net** convolutional neural network was used for segmentation. The architecture consists of:

* An encoder for hierarchical feature extraction
* A decoder with skip connections for spatial detail recovery

The model outputs a **binary segmentation mask** highlighting metastatic regions.

---

## Training Configuration

| Parameter      | Value                       |
| -------------- | --------------------------- |
| Model          | 2D U-Net                    |
| Input channels | 4                           |
| Image size     | 256 × 256                   |
| Optimizer      | Adam                        |
| Learning rate  | 0.001                       |
| Batch size     | 8                           |
| Epochs         | 20                          |
| Loss function  | Dice + Binary Cross-Entropy |
| Weight decay   | 1e-5                        |

---

## Dataset Split

| Split                 | Cases | 2D Slices |
| --------------------- | ----- | --------- |
| Training              | 90    | 8,705     |
| Validation            | 15    | 2,250     |
| Test (inference only) | 51    | 7,650     |

---

## Results

### Final Validation Metrics

| Metric     | Value      |
| ---------- | ---------- |
| Dice Score | **0.6152** |
| Precision  | **0.5216** |
| Recall     | **0.5892** |
| Accuracy   | **0.9994** |

### Interpretation

* The Dice score indicates a reasonable overlap between predicted and ground truth masks.
* High accuracy is expected due to strong background–foreground imbalance.
* Precision and recall show that the model successfully detects metastatic regions while maintaining moderate false positives.

---

## Test Inference

Since ground truth masks were not available for the test set:

* The trained model was used for **inference only**
* Predicted segmentation masks were generated and saved as NIfTI files
* Only test cases with complete MRI modalities were included

---

## Limitations

* A 2D model was used instead of a 2.5D or 3D architecture
* Some test cases were excluded due to missing MRI sequences
* Performance may improve with larger datasets and additional context across slices

---

## Conclusion

This project demonstrates the feasibility of using a **2D U-Net deep learning model** for automatic segmentation of brain metastases from multi-sequence MRI. Despite hardware constraints, the model achieved meaningful segmentation performance and validates the effectiveness of deep learning for this clinically relevant task.

---

## References

* Stanford AIMI Brain Metastasis Dataset
  [https://aimi.stanford.edu/datasets/brain](https://aimi.stanford.edu/datasets/brain)
* Grøvik et al., *Deep Learning Enables Automatic Detection and Segmentation of Brain Metastases on Multi-Sequence MRI*

