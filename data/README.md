---
license: cc-by-4.0
---

# Hanafuda Object Detection Dataset

This repository provides a specialized object detection dataset for Hanafuda (traditional Japanese playing cards). It contains 300 annotated images with a total of 2,232 bounding boxes, formatted for YOLO.

## Model & Training Code

The complete source code for training a YOLO model on this dataset, along with inference scripts and utilities, is available on GitHub:
**[tarumino3/Hanafuda-YOLO](https://github.com/tarumino3/Hanafuda-YOLO)**

## Dataset Characteristics

Unlike synthetic datasets, this collection captures the natural class imbalance inherent in real-world Hanafuda gameplay. 

- **Long-tail Distribution**: Common "Kasu" (plain) cards appear with much higher frequency. This is largely due to the structure of the Hanafuda deck: most months contain two "Kasu" cards (November has one, December has three). Although the "Kasu" cards within the same month have slight visual variations, they are grouped under a single label (e.g., all three December plain cards are labeled as `12-kas`). This grouping inherently inflates the instance count for "Kasu" classes compared to the strictly unique cards.
  - *High frequency example*: `12-kas` (encompasses 3 distinct card designs)
  - *Low frequency example*: `01-hkr-tsuru` (only 1 distinct card design)
- **Practical Scenarios**: The images include overlapping cards, hand-held cards, and scattered layouts to simulate actual inference conditions.
- **Card Deck Used**: The dataset was created using a standard physical deck of Nintendo Hanafuda cards.

## Statistics

- **Total Images**: 300
- **Total Annotations**: 2,232
- **Average Labels per Image**: 7.44
- **Format**: YOLO format (`class_id x_center y_center width height` normalized)
- **Classes**: 36 unique card classes (defined in `classes.txt`)

## Repository Structure

The dataset is intentionally provided as a single unified collection without a predefined train/val split. This allows researchers to implement their own cross-validation or data-splitting strategies.

```text
.
├── images/raw/      # 300 source images (.JPG)
├── labels/raw/      # 300 YOLO annotation files (.txt)
├── classes.txt      # Class index mapping
└── notes.json       # Export metadata
```

## Getting Started

You can download the dataset directly using the Hugging Face CLI:

```bash
# Install huggingface_hub if needed
pip install huggingface_hub

# Download the repository
hf download tarumino3/Hanafuda-Object-Detection --local-dir ./data --repo-type dataset
```

## Training Recommendations

Due to the inherent class imbalance, training a standard object detection model may result in a bias toward the majority classes. We recommend the following approaches:

1. **Data Splitting**: Randomly split the `images/` and `labels/` directories into training and validation sets (e.g., an 80/20 ratio) before starting your training pipeline.
2. **Handling Imbalance**: Implement Focal Loss or use a weighted random sampler for the minority classes.
3. **Data Augmentation**: Apply mosaic, rotation, and scaling augmentations to improve the detection robustness of rare cards.
4. **Evaluation Metrics**: Rely on mean Average Precision (mAP) to evaluate performance properly across all 48 classes, rather than global accuracy.