# 🏗️ Construction Site Object Detection & Zone Classification(YOLOv8)

## 📌 Overview

This project presents a **computer vision-based object detection system** for construction sites. The system detects workers and their Personal Protective Equipment (PPE) using YOLOv8 and performs **zone classification**  based on visual analysis of the scene.

The goal is to automatically identify construction personnel and safety equipment while categorizing work zones for safety monitoring applications.

---

## 🎯 Objectives

* Detect workers in construction environments
* Identify PPE (helmets and safety vests)
* Classify work zones into Zone A (high activity) or Zone B (standard activity)
* Generate structured output with bounding boxes and zone labels
* Provide foundation for safety compliance systems

---



## 📊 Dataset

### Source

* Custom construction site images (84 total images)
* Roboflow format with YOLO annotations

### Dataset Features

* Real construction site environments
* Mixed lighting conditions
* Multiple workers per image
* Various safety equipment configurations

### Classes

| Class ID | Label  | 
| -------- | ------ | 
| 0        | Person | 
| 1        | Helmet | 
| 2        | Vest   | 

### Data Split

| Split       | Images | Percentage |
| ----------- | ------ | ---------- |
| Training    | 58     | 69%        |
| Validation  | 12     | 14%        |
| Test        | 14     | 17%        |

---

## ⚙️ Data Preprocessing

* Verified label format (YOLO format .txt files)
* Checked image-label correspondence (all 84 images have matching labels)
* **Fixed class ordering** - swapped classes 0 and 1 to ensure correct mapping:
  * Class 0 → Person (correct)
  * Class 1 → Helmet (correct)
* Generated zone labels based on orange color analysis (safety cones/vests)

### Zone Classification Logic

| Zone  | Criteria                              |
| ----- | ------------------------------------- |
| Zone A | High orange content (>5000 pixels)    |
| Zone B | Low orange content (default)          |

---

## 🤖 Model Training

| Parameter     | Value               |
| ------------- | ------------------- |
| Model         | YOLOv8s             |
| Pretrained    | `yolov8s.pt`        |
| Image Size    | 640                 |
| Epochs        | 50                  |
| Batch Size    | 8                   |
| Patience      | 20                  |
| Optimizer     | AdamW (auto)        |

Training performed using **Google Colab with T4 GPU** .

### Training progress and Loss Reduction
<img width="1189" height="490" alt="image" src="https://github.com/user-attachments/assets/c93160bf-9650-4169-ba61-82b7ef9e047c" />


---

## 📈 Evaluation Metrics

### Overall Performance (Validation Set)

| Metric              | Score   |
| ------------------- | ------- |
| mAP@0.5             | 0.560   |
| mAP@0.75            | 0.409   |
| mAP@0.5:0.95        | 0.377   |
| Precision (mean)    | 0.799   |
| Recall (mean)       | 0.471   |

<img width="695" height="452" alt="image" src="https://github.com/user-attachments/assets/53b0cc70-a1ab-4341-a474-4a085b32fff9" />


### Per-Class Performance

| Class   | Precision | Recall | mAP@0.5 |
| ------- | --------- | ------ | ------- |
| Person  | 0.857     | 0.517  | 0.643   |
| Helmet  | 0.952     | 0.450  | 0.537   |
| Vest    | 0.588     | 0.444  | 0.502   |


### Key Observations

* **Highest precision** → Helmet detection (95% - very few false positives)
* **Best mAP** → Person detection (most reliable class)
* **Area for improvement** → Vest detection (limited training data)

---
# 🦺 Safety Check Logic

The system uses YOLO object detection to count workers, helmets, and vests, then applies zone-based safety rules.

## How it works

```python
 Zone A: Active zone - needs BOTH helmet AND vest
if zone == 'A':
    if helmets >= persons and vests >= persons:
        status = "✅ SAFE"
    else:
        status = "❌ UNSAFE"

Zone B: General zone - needs helmet ONLY
else:
    if helmets >= persons:
        status = "✅ SAFE"
    else:
        status = "❌ UNSAFE"
```
## 🔍 Inference & Output

The system produces:

1. **Bounding boxes** with class labels and confidence scores
2. **Zone classification** (A or B) for the scene
3. **Structured CSV output** (`zone_labels.csv`)

### Sample Detection Output
<img width="482" height="659" alt="image" src="https://github.com/user-attachments/assets/bd8b9986-6710-4665-9e40-df841b8b19b9" />

📍 ZONE: B
👷 Workers: 7
🪖 Helmets: 5
👕 Vests: 2

📊 CONFIDENCE: HIGH ✅
   Person: 75.73%
   Helmet: 71.97%
   Vest: 64.40%

⚠️ EDGE CASE WARNINGS:
   ⚠️ Distant worker - detection may be inaccurate

📋 STATUS: ❌ UNSAFE
📝 REASON: Missing: 2 helmets

📄 Report saved: report_WhatsApp Image 2026-04-01 at 8-46-23 AM_jpeg.rf.uZ1zhinqe6nxHPSCrc2U (2).txt
🖼️ Image saved: output_WhatsApp Image 2026-04-01 at 8-46-23 AM_jpeg.rf.uZ1zhinqe6nxHPSCrc2U (2).jpeg


---

## 🎥 Features

* Real-time object detection (images)
* Zone-based labeling (A / B)
* Visual bounding boxes with confidence scores
* Automatic label verification
* Export results to CSV

---

## ⚠️ Limitations
```
Limitation	                          Impact
Small dataset (only 84 images)	    Limited generalization
Class imbalance	                   Vest detection weaker (9 instances)
Small validation set (12 images)	  Metrics may not be fully reliable
```
## 🚀 Future Improvements
Collect more training data (target 500+ images per class)

Add data augmentation (rotation, scaling, lighting variations)

Implement real-time video tracking

Add safety rule engine (SAFE/UNSAFE classification)

Deploy as web application

Train larger model (YOLOv8m or YOLOv8l)

Add confidence threshold tuning interface

Integrate with live camera feeds

### Output Files

File	                 Description
best.pt	              Best model weights (22.5 MB)
last.pt	              Final epoch weights
results.csv     	     Training metrics per epoch
labels.jpg	           Label distribution visualization
zone_labels.csv	Zone  classification for training images

### Source Code
You can access all files from Google Drive:https://drive.google.com/drive/folders/1-ZZpNsP_hVN0rlQIvgvykr5IsrZ_qLcd?usp=sharing

## 📌 Conclusion
This project successfully implemented a YOLOv8-based object detection system for construction site monitoring using a limited dataset of only 84 self-collected images. Due to the small dataset size, the model's overall performance is modest, achieving a mean average precision (mAP@0.5) of 56%. However, the model demonstrates promising capabilities in detecting workers (64.3% mAP), helmets (53.7% mAP with 95% precision), and safety vests (50.2% mAP). While vest detection is weaker due to fewer training samples , the system successfully identifies key safety equipment and performs zone classification (Zone A/B) based on visual analysis. Despite performance limitations, this project serves as a valuable proof-of-concept demonstrating that even with minimal data, YOLOv8 can learn meaningful patterns for construction site monitoring. 
