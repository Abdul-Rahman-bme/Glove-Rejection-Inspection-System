# Research Reference Learning Notes

This file records what I learned from the research papers and how each idea can be used in the GRIP project.

Project focus:

- Glove sorting on a moving conveyor
- Left glove vs right glove detection
- Label or wrist print defect rejection
- Real-time camera based inspection
- SCARA-ready reject decision output

---

## 1. Research to Project Mapping

| Paper / PDF | Main idea of the paper | What is useful for GRIP | How I will use it | Directly use or adapt? |
|---|---|---|---|---|
| Learning Manufacturing Computer Vision Systems Using Tiny YOLOv4 | Shows a practical computer vision workflow for manufacturing: dataset creation, annotation, training, testing, and microcomputer deployment | Useful for planning the full project workflow, not only the model | Use it to justify the overall development process: collect dataset, label images, train YOLO, test live camera, deploy into a manufacturing-like setup | Adapt |
| Glove Defect Detection via YOLOv5 | Uses YOLOv5 for glove defect detection and compares YOLOv5 with other object detection models | Useful as the strongest reference for using YOLO on glove-related inspection | Use YOLO for glove detection, left/right classification support, and fast real-time inference baseline | Directly useful |
| Identification of Sticker Printing Defects in Glove Manufacturing Process Using Computer Vision Techniques | Gives a glove sticker defect inspection framework using teaching mode, inspection mode, preprocessing, fixed ROI, template comparison, and defect decision logic | Most useful reference for the label defect rejection part | Use a template based label inspection pipeline after cropping the label ROI from the detected glove | Very directly useful |
| A Fast and Adaptive Road Defect Detection Approach Using Computer Vision with Real Time Implementation | Uses classical image processing for fast defect detection: adaptive preprocessing, median filtering, thresholding, morphology, feature extraction, and classification | Useful for preprocessing, image enhancement, noise removal, and real-time defect feature extraction | Use similar preprocessing ideas to improve label ROI before defect comparison | Adapt |

---

## 2. What I Can Learn From Each Paper

### 2.1 Learning Manufacturing Computer Vision Systems Using Tiny YOLOv4

**What they did**

They presented a manufacturing computer vision learning framework. The work explains how to move from theory to practical implementation using object detection, dataset preparation, training, testing, and live camera deployment.

**Useful ideas for GRIP**

| Useful idea | Why it matters for GRIP | My implementation plan |
|---|---|---|
| Project-based workflow | GRIP is not only a model, it is a complete inspection system | Keep README structured around dataset, training, testing, deployment, and evaluation |
| Manufacturing use cases | The paper connects CV to sorting, quality control, material handling, and robotic guidance | Use this to justify glove sorting and SCARA removal as manufacturing automation |
| Dataset creation and annotation | Accurate labels are needed before training YOLO | Use Roboflow or LabelImg style workflow for glove bounding boxes and label ROI data |
| Data augmentation | Conveyor lighting, camera angle, blur, and occlusion change in real use | Add brightness, exposure, rotation, blur, noise, and background variation augmentation |
| Lightweight YOLO models | Real-time systems need fast inference | Use YOLOv8n, YOLO11n, or ONNX exported small models instead of heavy models |
| Deployment mindset | A model must be tested through a camera, not only on saved images | Keep live camera testing and video testing as part of the pipeline |

**How this improves my project**

This paper helps me organize GRIP like a real engineering project. It supports the idea that the final output should not only be a trained model, but a working system with camera input, live inference, reject decision, and hardware output.

---

### 2.2 Glove Defect Detection via YOLOv5

**What they did**

They trained YOLOv5 to detect glove classes such as normal glove, tear glove, and unstripped glove. They used data preparation, augmentation, train-validation-test splitting, and performance comparison against other detection models.

**Useful ideas for GRIP**

| Useful idea | Why it matters for GRIP | My implementation plan |
|---|---|---|
| YOLO is suitable for glove inspection | My first stage is glove detection and sorting | Continue using YOLO for glove bounding box detection |
| Small model for speed | Conveyor system needs fast inference | Use YOLOv8n or YOLO11n and export to ONNX |
| Data augmentation | Real production data can be limited | Augment only the training set with exposure, noise, blur, and rotation |
| Train-validation-test split | Prevents overclaiming model performance | Split real images first, then augment training only |
| Model comparison metrics | Need to prove the model is good | Track mAP, precision, recall, inference time, FPS, model size, and false detection rate |
| Class imbalance handling | Defect samples will be fewer than good samples | Balance label defect and non-defect classes as much as possible |

**How this improves my project**

This paper supports using YOLO as the main detector for glove-level detection. For GRIP, YOLO should detect the glove and classify the main glove status, while a separate label inspection module should handle small print defects.

**Important limitation**

This paper detects glove defects at a larger glove level. My project also needs small printed label inspection, so I should not depend only on YOLO for label quality unless I collect a strong label defect dataset.

---

### 2.3 Sticker Printing Defects in Glove Manufacturing Process

**What they did**

They created a specific computer vision framework for detecting sticker or label printing defects on gloves. The system uses a reference artwork, fixed camera setup, preprocessing, geometric correction, multi-level inspection, object significance, error evaluation, and quality measurement.

**Most useful ideas for GRIP**

| Useful idea | Why it matters for GRIP | My implementation plan |
|---|---|---|
| Teaching mode | Stores reference data before live inspection | Create a `label_template/` folder with good label reference images and extracted features |
| Inspection mode | Performs fast checking during production | Run label inspection only after the glove is detected and label ROI is cropped |
| Fixed ROI | Reduces unnecessary processing | Crop only the wrist label region, not the whole frame |
| Grayscale + thresholding | Label print is usually high contrast | Convert label ROI to grayscale and binary image before comparison |
| Geometric checks | Label can be shifted, rotated, or scaled | Check label displacement, rotation, scale, and bounding box alignment |
| Foreign object detection | Extra ink or smudge can appear | Remove expected template area and detect extra blobs |
| Missing object detection | Some text or symbols may not print | Compare template objects against inspection objects |
| Object significance | Some label content matters more than others | Give higher importance to size, safety standard, and model name than decorative marks |
| Decision function | Not every small defect should reject the glove | Reject only if defect score is above allowed threshold |
| Quality score | Useful for reporting and audit | Output a label quality score and reject reason |

**How this improves my project**

This is the most important reference for the label rejection module. The best design for GRIP is:

```text
YOLO glove detection
        ↓
label ROI crop
        ↓
template based label inspection
        ↓
defect score
        ↓
ACCEPT / REJECT decision
```

**My label defect classes or reject reasons**

| Reject reason | How to detect |
|---|---|
| Missing label | Label ROI has too few dark/printed pixels |
| Label displaced | Label bounding box is outside expected region |
| Label rotated | Minimum area rectangle angle exceeds threshold |
| Label scaled | Printed region area differs from reference |
| Smudge or inkblot | Extra connected components or large blobs appear |
| Missing text or logo part | XOR or template difference shows missing regions |
| Blurry label | Low Laplacian blur score or weak edge strength |
| Low contrast / faded print | Histogram and thresholded foreground area too weak |

---

### 2.4 Fast and Adaptive Road Defect Detection

**What they did**

They proposed a fast classical image processing pipeline for road defect detection. The work focuses on image enhancement, adaptive preprocessing, median filtering, thresholding, morphology, feature extraction, and decision-tree style classification.

**Useful ideas for GRIP**

| Useful idea | Why it matters for GRIP | My implementation plan |
|---|---|---|
| Adaptive preprocessing | Factory lighting and glove colors can vary | Calculate brightness or background statistics before thresholding |
| Median filtering | Helps reduce noise while keeping edges | Use median blur on label ROI before thresholding |
| Image enhancement | Defect regions need to stand out | Use contrast enhancement or threshold tuning on label ROI |
| Morphological operations | Small gaps and noise affect defect detection | Use erosion, dilation, opening, and closing after binary conversion |
| Feature extraction | Defects can be described numerically | Extract area, connected components, solidity, bounding box, angle, and foreground ratio |
| Simple decision logic | Fast and explainable | Use rule-based rejection for obvious label defects before deep learning classifier |

**How this improves my project**

This paper is not about gloves, but the preprocessing idea is useful. It helps make the label inspection more reliable under changing light and noise.

---

## 3. Final GRIP Pipeline Based on the Four References

```text
1. Camera capture
   - fixed top-down camera
   - short exposure
   - controlled LED lighting
   - blur score calculation

2. Frame quality check
   - reject or skip blurry frames
   - log blur score
   - check brightness and contrast

3. Glove detection and sorting
   - YOLO detects glove bounding box
   - classify left_glove or right_glove
   - wrong hand triggers reject command

4. Tracking
   - ByteTrack or simple centroid tracking
   - avoid duplicate robot picks
   - estimate belt position

5. Label ROI extraction
   - crop wrist label from glove bbox
   - better option: use label-corner keypoints
   - save ROI image for debugging

6. Label preprocessing
   - grayscale
   - denoise
   - threshold
   - morphology
   - optional geometric correction

7. Label defect inspection
   - compare against template
   - detect missing parts
   - detect foreign blobs
   - detect displacement, rotation, and scaling
   - calculate defect score

8. Final decision
   - PASS
   - PICK_RIGHT_GLOVE
   - PICK_LABEL_DEFECT
   - MANUAL_INSPECTION

9. Output
   - JSON command to STM32 or SCARA controller
   - save frame, ROI, prediction, confidence, reject reason
```

---

## 4. What I Should Add to the GitHub Repository

Recommended folder:

```text
docs/
└── research_learning/
    ├── research_reference_learning.md
    ├── paper_01_manufacturing_yolo_notes.md
    ├── paper_02_glove_yolov5_notes.md
    ├── paper_03_sticker_defect_notes.md
    └── paper_04_adaptive_defect_preprocessing_notes.md
```

For now, this file can be saved as:

```text
docs/research_learning/research_reference_learning.md
```

---

## 5. README Section to Add

```markdown
## Research-Based Development Direction

This project is being developed by combining ideas from four computer vision references.

- YOLO-based manufacturing inspection is used as the base idea for real-time object detection, dataset collection, annotation, model training, and deployment.
- The glove defect detection YOLOv5 paper supports using YOLO for glove-level detection and fast inference.
- The glove sticker printing defect thesis is used as the main reference for label defect rejection using template-based inspection, fixed ROI, geometric checks, missing object detection, foreign object detection, and quality scoring.
- The adaptive road defect detection paper is used for classical preprocessing ideas such as median filtering, thresholding, morphology, and fast feature extraction.

The final GRIP system uses a hybrid approach:
YOLO handles glove detection and sorting, while classical/template-based computer vision handles small printed label defects.
```

---

## 6. Development Checklist From Research

| Task | Inspired by | Status |
|---|---|---|
| Create a research learning folder | All papers | To do |
| Add this summary file to GitHub | All papers | To do |
| Keep YOLO for glove detection | YOLOv5 glove paper | In progress |
| Add proper train-val-test split | YOLOv5 glove paper | To verify |
| Augment only training set | YOLOv5 and manufacturing YOLO papers | To verify |
| Add negative/background images | Manufacturing YOLO paper | To do |
| Add label ROI crop module | Sticker defect thesis | In progress |
| Add good label template storage | Sticker defect thesis | To do |
| Add grayscale and threshold preprocessing | Sticker defect thesis, road defect paper | To do |
| Add morphology cleaning | Sticker defect thesis, road defect paper | To do |
| Add geometric checks for label | Sticker defect thesis | To do |
| Add missing label and smudge detection | Sticker defect thesis | To do |
| Add blur score and frame quality logging | Road defect paper and project testing need | In progress |
| Add final reject reason in JSON | GRIP system requirement | To do |
| Add evaluation table | YOLOv5 glove paper | To do |

---

## 7. Metrics I Should Report

| Module | Metrics to report |
|---|---|
| Glove detection | mAP50, precision, recall, false positives, false negatives |
| Left/right sorting | accuracy, confusion matrix, wrong-hand miss rate |
| Label ROI crop | crop success rate, average crop time |
| Label defect inspection | false accept rate, false reject rate, defect recall |
| Runtime | FPS, inference time, preprocessing time, total decision latency |
| Conveyor integration | pick timing error, duplicate pick count, missed pick count |

---

## 8. Final Research-Based Design Decision

The strongest design direction is a hybrid system:

```text
Deep learning for large object detection
+
Classical computer vision for small label defect inspection
+
Rule-based final decision for robotic rejection
```

Reason:

- YOLO is strong for detecting gloves and glove position.
- Printed label defects are small and detail-sensitive, so template comparison and preprocessing are more explainable.
- Rule-based reject reasons are easier to debug and safer for a robotic rejection system.
- The system can be improved later by replacing the label defect module with a trained classifier if enough real label defect data is collected.

---

## 9. Short Summary for GitHub

GRIP is developed using a research-backed hybrid computer vision approach. YOLO is used for real-time glove detection and sorting, while template-based image processing is used for printed wrist-label defect inspection. The research papers guided the dataset workflow, augmentation strategy, real-time deployment structure, label ROI inspection design, and preprocessing methods such as thresholding, median filtering, and morphological operations.
