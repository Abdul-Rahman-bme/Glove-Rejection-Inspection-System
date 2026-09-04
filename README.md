# GRIP - Glove Rejection and Inspection Process

## Current Project Status

GRIP is an active engineering research prototype for automated glove inspection on a conveyor belt.

The project has progressed through several computer-vision approaches, including:

1. YOLO glove detection
2. direct left/right YOLO classification
3. crop-based CNN classification
4. YOLO-pose/keypoint experiments
5. YOLO11n three-class detection
6. current two-stage vision pipeline: YOLO11n-Seg + event processing + MobileNetV3-Small

The current architecture intentionally separates:

- Layer 1: glove localisation and segmentation
- Layer 2: left/right chirality classification

This avoids forcing the detector to learn glove handedness and allows the same accepted glove crop to be classified exactly once per physical passage.

---

## Current Vision Pipeline

Camera / recorded conveyor video
    |
    v
Camera-specific ROI
    |
    v
YOLO11n-Seg
single class: glove
    |
    v
Valid segmentation mask
+ tight mask bounding box
    |
    v
Physical-size filtering
    |
    v
Trigger-zone eligibility
    |
    v
Trigger-aware PassageProcessor
    |
    v
Temporal confirmation
association
best-frame selection
    |
    v
Canonical event crop
tight bbox + aspect-preserving letterbox
256 x 256
    |
    v
MobileNetV3-Small
left / right classifier
    |
    v
Final chirality decision

A core design requirement is:

One physical glove passage should produce one accepted event and one classifier call.

The same crop-generation logic is used for dataset extraction and intended deployment to reduce train/deployment mismatch.

---

## Layer 1 - YOLO11n-Seg Glove Detector V2

The current detector is a single-class glove instance-segmentation model.

It does not classify left/right handedness.

### Validation Set

- 64 validation images
- 95 glove instances

### Validation Results

| Metric | Bounding Box | Segmentation Mask |
|---|---:|---:|
| Precision | 0.981 | 0.986 |
| Recall | 0.895 | 0.905 |
| mAP@0.50 | 0.963 | 0.966 |
| mAP@0.50:0.95 | 0.913 | 0.906 |

Training stopped at epoch 68 through early stopping, with the best checkpoint obtained at epoch 48.

These values describe detector validation performance only. They are not end-to-end factory-system accuracy.

Selected detector results are stored under:

results/current/detector_yolo11n_seg_v2/

---

## Passage Processing

Detection alone is not enough for conveyor operation because the same physical glove may appear across many consecutive frames.

The current pipeline therefore uses a trigger-aware passage processor that performs:

- temporal confirmation
- detection association
- best-frame selection
- missing-frame exit logic
- cooldown handling
- multiple-detection rejection
- trigger-zone eligibility
- physical-size filtering

This stage converts a stream of frame-level detections into a single accepted glove event.

---

## Canonical Crop Generation

For each accepted passage:

1. select the best source frame
2. use the tight detector bounding box
3. preserve the glove aspect ratio
4. letterbox the crop
5. produce a 256 x 256 canonical event image

The classifier then receives a resized 224 x 224 input.

The same crop-generation function is used for:

- classifier dataset extraction
- intended deployment inference

This is important because using different crop logic during training and deployment can create avoidable domain mismatch.

---

## Layer 2 - MobileNetV3-Small Chirality Classifier V1

The current Layer-2 baseline is a pretrained MobileNetV3-Small classifier.

### Training Dataset

| Class | Crops |
|---|---:|
| Left | 3094 |
| Right | 577 |
| Total | 3671 |

Class-weighted cross-entropy was used to compensate for the numerical class imbalance.

Horizontal flipping was disabled because mirroring a glove changes chirality.

### Validation Dataset

| Class | Crops |
|---|---:|
| Left | 351 |
| Right | 301 |
| Total | 652 |

### Best-Epoch Validation Results

| Metric | Value |
|---|---:|
| Accuracy | 0.9985 |
| Macro Recall | 0.9986 |
| Macro F1 | 0.9985 |
| Left Recall | 0.9972 |
| Right Recall | 1.0000 |

### Confusion Matrix

| True / Predicted | Left | Right |
|---|---:|---:|
| Left | 350 | 1 |
| Right | 0 | 301 |

Only one validation crop was misclassified.

However, 99.85% must not be presented as final factory accuracy.

The validation set was used during model selection, and the current dataset does not yet provide enough independent recording-session diversity for a strong production-generalisation claim.

Selected classifier results are stored under:

results/current/chirality_mobilenet_v1/

---

## Classifier Diagnostic Analysis

The single validation error was investigated using several interpretability and ablation methods.

### Grad-CAM

Grad-CAM was used to inspect class-sensitive spatial regions.

Because Grad-CAM is coarse and class-dependent, it was not treated as proof of a particular shortcut.

### Occlusion Sensitivity

A causal occlusion test showed that masking regions around the finger/thumb geometry produced the largest reduction in the incorrect right prediction.

For the strongest tested patch:

P(right): approximately 0.826 -> 0.026

This suggests the wrong prediction depended strongly on glove geometry rather than simply on background appearance.

### Validation-Wide Region Ablation

| Ablated Region | Accuracy | Left Recall | Right Recall |
|---|---:|---:|---:|
| Original | 0.9985 | 0.9972 | 1.0000 |
| Finger side | 0.9647 | 0.9886 | 0.9369 |
| Cuff side | 0.9939 | 0.9915 | 0.9967 |
| Centre | 0.9617 | 0.9886 | 0.9302 |
| Top background | 0.9985 | 0.9972 | 1.0000 |
| Bottom background | 0.9985 | 0.9972 | 1.0000 |

The current classifier is substantially more sensitive to glove geometry than to the tested top/bottom background regions.

This is encouraging, but it does not prove that every possible visual shortcut has been eliminated.

Diagnostic files are stored under:

results/current/chirality_mobilenet_v1/diagnostics/

---

## Current Limitations

The current system is not yet claimed to be production-ready.

Important limitations include:

- the classifier training data are not sufficiently diverse across independent right-hand sessions
- current validation data come from the same general acquisition period
- the current validation set was used for model selection
- camera-specific ROI and trigger geometry must be calibrated for the actual deployment camera
- detector metrics do not directly measure end-to-end rejection performance
- classifier confidence values are not necessarily calibrated probabilities
- new independent conveyor recordings are required for a proper locked final test

These limitations are the reason the project is moving toward a new multi-session dataset before selecting a final Layer-2 architecture.

---

## Next Dataset and Model Evaluation

The next data-collection stage is designed around independent recording sessions, not random adjacent frames.

Each useful session should contain both left and right gloves under the same recording conditions so that glove class is not confounded with recording session.

The new dataset will include variation such as:

- different recording sessions
- normal lane assignments and lane swaps
- different times of day
- natural lighting changes
- glove placement and rotation variation
- different glove batches where available
- normal production variation

A session-level split will be used for:

- training
- validation
- locked testing

Candidate Layer-2 models may include:

- MobileNetV3-Small
- ResNet18
- ConvNeXtV2-Pico
- Swin-T
- DINOv3 ConvNeXt-Tiny

All candidate models should use the same canonical crops and the same session-level evaluation protocol.

The current MobileNetV3-Small remains the baseline until another model demonstrates better performance on independent data.

---

## Repository Structure

Glove-Rejection-Inspection-System/
|
|-- README.md
|-- current_pipeline/
|   |-- configs/
|   |-- docs/
|   |-- models/
|   |-- scripts/
|   |-- src/
|   |-- tests/
|   |-- tools/
|   `-- ...
|
|-- results/
|   |-- current/
|   |   |-- detector_yolo11n_seg_v2/
|   |   `-- chirality_mobilenet_v1/
|   |
|   `-- legacy/
|       |-- 2026-06-28_yolo_pose/
|       |-- 2026-06-30_workflow/
|       |-- 2026-07-01_yolo_3class/
|       |-- old_test_outputs/
|       `-- trial_1/
|
|-- Camera_Mount_setup/
|-- Codes/
|-- Documentation/
`-- References/

---

## Development History

Earlier GRIP approaches are preserved under:

results/legacy/

These include:

### June 28 - YOLO Pose

Keypoint-based glove pose experiments.

Main limitation:

- empty gloves are highly deformable
- fingers and thumbs are frequently hidden
- keypoint visibility is inconsistent
- pose alone was not robust enough for the immediate factory requirement

### June 30 - Keypoint / Annotation Workflow

Development of annotation tools and pose-related workflow experiments.

### July 1 - YOLO11n Three-Class Detection

Direct detector classes:

left_glove
right_glove
unclear_glove

This provided a useful prototype but was later replaced by the current modular architecture.

The current design separates glove detection from chirality classification because this gives better control over event processing, crop generation, classifier evaluation, and future model replacement.

---

## Results Policy

The repository intentionally stores only selected experiment evidence.

Included:

- important metric CSV files
- training configuration summaries
- confusion matrices
- precision/recall/F1 curves
- selected diagnostic visualisations
- interpretability and ablation results

Not included:

- raw conveyor videos
- full extracted datasets
- temporary frame dumps
- complete virtual environments
- caches
- repeated model checkpoints
- large temporary inference outputs

Raw recordings and large datasets are maintained separately from GitHub.

---

## Model Checkpoints

The current main checkpoints are:

yolo11n_seg_glove_final_v2.pt
chirality_mobilenet_v3_small_aug27_v1.pt

Model binaries should preferably be distributed through a dedicated release or model-artifact mechanism instead of being repeatedly duplicated inside the Git repository.

---

## Pipeline Source

The current pipeline is maintained as a dedicated development repository and imported into this project repository under:

current_pipeline/

This allows the project repository to preserve:

- project history
- current results
- documentation
- hardware-related work
- research evolution

while the development repository remains focused on the reusable vision pipeline itself.

---

## Long-Term System Goal

The full GRIP system is intended to evolve toward:

Camera
  |
  v
Vision inspection pipeline
  |
  v
Glove event + chirality decision
  |
  v
Reject / accept decision
  |
  v
Robot-control interface
  |
  v
SCARA rejection mechanism

The current development priority is to establish reliable computer-vision performance before integrating the final robot-rejection stage.

---

## Current Priority

The next major milestone is:

Collect a more diverse multi-session conveyor dataset, train and compare multiple Layer-2 classifiers under the same protocol, and evaluate them on an untouched session-level test set.

Only after that evaluation should the project decide whether MobileNetV3-Small remains the final classifier or should be replaced.

---

## Notes

This repository documents an active engineering research project. Results and architecture may change as additional independent conveyor data are collected and evaluated.
