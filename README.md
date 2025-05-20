# 🧠 Instance Segmentation Benchmark — YOLOv8 vs Mask R-CNN

## 📘 Project Overview

This project compares two state-of-the-art architectures for **instance segmentation**:

- [x] **YOLOv8** (Ultralytics)
- [x] **Mask R-CNN** (Torchvision, ResNet-50 FPN backbone)

Both models were evaluated through fine-tuning and training on different versions of a **customized Cityscapes dataset**, adapted specifically for instance-level mask prediction.

---

## 📂 Dataset Preparation

Cityscapes, in its original form, was not directly suited for instance segmentation.  
To overcome this, we restructured it using **Roboflow** (Instance Segmentation v3.0, Fast). This allowed us to generate high-quality masks and balance the class distributions.

Each dataset version was exported in **YOLOv8 format** with:

- **Resolution**: 640×640 (stretched)
- **No augmentations applied**
- **Split**: train/val/test sets with various class configurations

> The dataset is available in a dedicated repository:  
> 📦 [Cityscapes Instance Dataset (10 Versions)](https://github.com/yourusername/cityscapes-instance-dataset)

---

## 📊 Dataset Summary

| Version | # Classes | Total | Train | Val | Test |
|---------|-----------|--------|--------|-----|------|
| v1–v2   | 8         | 132    | 86     | 26  | 20   |
| v3–v4   | 39        | 2641   | ~1970  | ~590| ~70  |
| v5–v9   | 10–26     | 2641   | 2000   | 421 | 220  |
| v10     | 34        | 1994   | 1623   | 371 | 0    |

Versions were crafted to test different complexity levels, from minimal examples to production-level configurations.

---

## ⚙️ Architectures and Training

### Mask R-CNN

- **Backbone**: ResNet-50 FPN
- **Losses**: Multi-task (classification + bounding box + mask loss)
- **Fine-tuned on v10** with 20 epochs
- Experiments with optimizers: `Adam`, `SGD`, batch sizes 4/6/12

### YOLOv8

- **Model**: `yolov8s-seg.pt`
- **Training from scratch** on v3, v4, v9, v10
- Experiments included:
  - Optimizers: `Adam`, `SGD`
  - Batch sizes: 6 / 16
  - Epochs: 15 / 25
  - Learning rates: `1e-2`, `1e-3`

---

## 🧪 Key Findings

Based on extensive training and prediction comparisons (test set, class-wise detection):

### Mask R-CNN

✅ Detected **small instances** (e.g., people in background)  
✅ More **consistent and interpretable masks**  
⚠️ Slightly more **sensitive to false positives**, especially with larger batch sizes  
✅ Preferred optimizer: **Adam**  
⚠️ Training is slower but **converges more accurately**

### YOLOv8

✅ Faster convergence  
✅ Detects **more total instances** — especially in large, visible classes like `vegetation`, `building`, `car`  
⚠️ More prone to **redundant detections / false positives**  
✅ Performance improves significantly with **larger datasets**  
⚠️ Less precise on small-scale objects (e.g., distant `person`, `polegroup`):contentReference[oaicite:0]{index=0}

---

## 🔍 Visual Comparison

- **With 600 images**, both networks showed competent performance but YOLO produced more detections, sometimes redundant.
- **With 2000 images**, YOLO detects more `vegetation`, `terrain`, `pole` instances but also introduces some false positives.
- **Mask R-CNN** retains better granularity and clarity on difficult regions like overlapping or distant people.

---

## 📈 Training Loss Observations

**Mask R-CNN**:  
- Classification loss (Adam): ~20 at 20th epoch  
- Box + Mask losses: consistent, stable, ~1–2 unit gap in favor of Adam over SGD:contentReference[oaicite:1]{index=1}

**YOLOv8**:  
- Faster validation improvement, especially with `Adam`  
- Better results with 25 epochs + larger batch size + rich datasets (v10)

## 📌 Conclusions

- **YOLOv8** is a great choice when speed is a priority, but needs careful tuning to avoid over-detection.
- **Mask R-CNN** is more robust for dense, small-object environments — especially in urban scenes.
- The custom **Cityscapes-Instance** dataset proved crucial for adapting these models and balancing results.

---

## 📄 License

All code is released under MIT License.  
The dataset is adapted from Cityscapes and follows the original [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) license.
