# Image Restoration and Object Detection Pipeline

## Overview

This project implements an end-to-end pipeline that integrates image restoration (deblurring) with object detection to systematically analyse how image quality affects detection performance.

The pipeline evaluates detection across three domains:

- Blurred images (degraded input)
- Deblurred images (restored input)
- Sharp images (ground truth reference)

To ensure computational feasibility, a subset of 200 images was selected from a larger dataset (~5000 images).

---

## Objectives

- Investigate how motion blur affects object detection performance  
- Evaluate whether image restoration can improve detection results  
- Compare detection performance across blur, deblur, and sharp domains  
- Compare different YOLOv8 model variants  
- Fine-tune a detector using restored images  

---

## Dataset

This project is based on the COCO dataset (instances_val2017.json).

Due to computational limitations, a subset of 200 images containing "person" instances was selected.

Selection criteria:

- Each image contains at least one "person" object  
- Images are matched between blur and sharp domains using filename  
- Corresponding labels are extracted from COCO annotations  
- Labels are converted into YOLO format for training and evaluation  

This subset allows efficient experimentation while maintaining meaningful evaluation.

---

## Project Structure

coco/  
  blur/        # Blurred images  
  sharp/       # Ground truth images  
  label/       # COCO annotations  

outputs/  
  deblur/              # Restored images  
  processed_dataset/   # Train/val/test split  
  analysis/            # CSV results and plots  
  runs/                # YOLO training outputs  

---

## Pipeline Overview

### 1. Data Loading and Pair Matching
- Load blur and sharp images  
- Match images using filename  
- Validate image consistency  

### 2. Blur Level Estimation
- Compute blur score using Laplacian variance  
- Categorise images into blur levels  
- Enable stratified sampling  

### 3. Image Deblurring
Implemented methods:

- Unsharp Masking  
- Wiener Filtering  
- Richardson–Lucy  

Evaluation metrics:

- PSNR  
- SSIM  
- Runtime  

The best-performing method was selected for downstream processing.

### 4. Dataset Construction
- Extract labels from COCO JSON  
- Convert annotations into YOLO format  
- Perform stratified train/val/test split  
- Export processed dataset  

### 5. Object Detection Evaluation
- Compare pretrained models: YOLOv8n vs YOLOv8s  
- YOLOv8s was selected based on performance  
- Evaluate detection across:
  - blur
  - deblur
  - sharp  

Evaluation metrics:

- Precision  
- Recall  
- mAP50  
- mAP50-95  

### 6. Fine-Tuning
- Train YOLOv8s on the deblur dataset  
- Save checkpoints and logs  
- Re-evaluate across all domains  

---

## Results Summary

| Domain | Precision | Recall | mAP50 | mAP50-95 |
|--------|----------|--------|-------|----------|
| Blur   | 0.836    | 0.342  | 0.399 | 0.246    |
| Deblur | 0.764    | 0.343  | 0.406 | 0.251    |
| Sharp  | 0.709    | 0.543  | 0.613 | 0.437    |

---

## Key Findings

- Sharp images achieved the strongest overall detection performance  
- Deblurred images showed partial improvement, but gains were not consistent across all metrics  
- Blurred images achieved the highest precision due to conservative predictions  
- Recall and mAP improved significantly with increasing image clarity  
- YOLOv8s consistently outperformed YOLOv8n and was selected as the final model  

---

## Critical Analysis

The results reveal a clear precision–recall trade-off:

- Blurred images → fewer predictions → fewer false positives → higher precision  
- Sharp images → more predictions → higher recall and mAP  

The improvement from blur to deblur is limited, indicating:

- Classical deblurring methods cannot fully recover fine visual details  
- Restoration may introduce artefacts that affect detection performance  

These findings suggest that restoration can assist detection, but its effectiveness depends on both the evaluation metric and the image domain.

---

## Statistical Analysis

A Wilcoxon signed-rank test was applied to image-level detection scores to evaluate whether performance differences across domains were statistically significant.

This analysis strengthens the reliability of the results by validating that observed differences are not due to random variation.

---

## Reproducibility

- Fixed random seed  
- Stratified dataset split  
- Deterministic pipeline design  
- All outputs saved (CSV + visualisations)  

---

## AI Usage

AI tools (e.g., ChatGPT) were used for:

- Code generation and optimisation  
- Debugging and pipeline design  
- Conceptual explanations  

All outputs were reviewed, validated, and integrated manually.

See ai_prompt_log.md for details.

---

## Limitations

- Only 200 images used (subset of ~5000)  
- Limited computational resources  
- Classical deblurring methods have limited restoration capability  
- Restoration artefacts may affect detection performance  
- Results may not generalise to larger datasets  

---

## Future Work

- Apply deep learning-based deblurring methods  
- Increase dataset size  
- Improve restoration quality  
- Explore domain adaptation techniques  

---

## How to Run

Install dependencies:

pip install ultralytics opencv-python numpy pandas matplotlib scikit-image

Run notebook:

jupyter notebook

Execute all cells sequentially.

---

## Outputs

- Processed dataset: outputs/processed_dataset  
- Analysis results: outputs/analysis  
- Model training results: outputs/runs  
