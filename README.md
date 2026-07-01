# 🧤 GRIP - Glove Rejection and Inspection Process

<div align="center">

![GRIP Banner](https://img.shields.io/badge/GRIP-Glove%20Rejection%20%26%20Inspection%20Process-0D9488?style=for-the-badge&labelColor=0F172A)

[![Status](https://img.shields.io/badge/Status-Active%20MVP%20Research-F59E0B?style=flat-square)](.)
[![Latest Workflow](https://img.shields.io/badge/Latest%20Workflow-YOLO11n%203--Class%20Detect-2563EB?style=flat-square)](.)
[![Dataset](https://img.shields.io/badge/Dataset-Factory%20Video%20Frames-0D9488?style=flat-square)](.)
[![Platform](https://img.shields.io/badge/Platform-PC%20Training%20%E2%86%92%20Jetson%20Orin%20Nano-7C3AED?style=flat-square)](.)
[![University](https://img.shields.io/badge/ENTC-University%20of%20Moratuwa-1B3A6B?style=flat-square)](.)

**Automated Computer Vision + SCARA Robotic Quality Control System**  
*Real-time glove orientation inspection, uncertainty rejection, and belt-synchronised robotic removal*

---

### Group MOSFET · ENTC, University of Moratuwa · 2026

| Index | Name |
|---|---|
| 230212H | L.U.A. Gunasekara |
| 230171E | C.D. Elapatha |
| 230470U | T.S.R. Peiris |
| 230318M | J.H.D. Kariyawasam |
| 230507R | M.F.A. Rahman |

</div>

---

## 📌 Current Project Status

GRIP is an **active engineering MVP** for automated glove inspection on a conveyor belt. The project has gone through multiple computer-vision approaches:

1. YOLO glove detection
2. YOLO left/right object classification
3. CNN crop classification
4. YOLO-pose/keypoint experiments
5. **Latest urgent prototype: YOLO11n 3-class detection: `left_glove`, `right_glove`, `unclear_glove`**

The latest trained model is a **YOLO detect model**, not a pose model. It was trained as a fast MVP test because many glove frames are crumpled, blurred, or missing visible keypoints. The current best checkpoint is:

```text
1st July Workflow/best.pt
```

The system is **not production-ready yet**, but the latest workflow gives a practical baseline for testing whether left/right/unclear classification is learnable from the current factory video frames.

---

## 🆕 Latest Update - 1st / 2nd July Workflow

Today’s work focused on creating a quick, testable 3-class YOLO detection prototype from the annotated factory frames.

### What was done today

| Step | Completed work |
|---|---|
| 1 | Annotated factory frames using the custom GRIP YOLO-pose annotator UI. |
| 2 | Decided not to depend on pose/keypoints for the urgent test because many gloves have hidden fingers/thumbs and incomplete keypoints. |
| 3 | Used the already annotated labels but converted them into YOLO detect format by keeping only bbox + class. |
| 4 | Created a 70/20/10 dataset split. |
| 5 | Trained a YOLO11n detect model in Google Colab. |
| 6 | Stored the best validation model as `best.pt`. |
| 7 | Generated confusion matrices, result curves, prediction CSVs, and model checkpoints. |
| 8 | Uploaded the trained workflow outputs into the GitHub folder `1st July Workflow/`. |
| 9 | Created a video detector tester script for testing `best.pt` on a separate mixed video dataset. |

### Annotated class counts before split

| Class | Count |
|---|---:|
| `left_glove` | 117 |
| `right_glove` | 114 |
| `unclear_glove` / crumpled | 377 |
| **Total** | **608** |

### Dataset split created

| Split | Images |
|---|---:|
| Train | 425 |
| Validation | 121 |
| Test | 62 |
| **Total** | **608** |

During Colab training, the training set was balanced/expanded for the minority `left_glove` and `right_glove` classes:

| Stage | Images |
|---|---:|
| Original train split | 425 |
| Balanced training set used by notebook | 825 |

### Training configuration

| Item | Value |
|---|---|
| Model | `yolo11n.pt` |
| Task | YOLO Detect |
| Classes | `left_glove`, `right_glove`, `unclear_glove` |
| Image size | 640 |
| Max epochs | 100 |
| Early stopping patience | 20 |
| Horizontal flip | Disabled |
| Reason for disabling flip | Horizontal flip changes glove handedness and can corrupt labels |

### Important safety metric

The most important error is:

```text
true right_glove predicted as left_glove
```

This is dangerous because the wrong-hand glove could pass as a good left glove. For this project, this error matters more than overall accuracy.

---

## 📊 Latest YOLO Detect Prototype Results

Result files were generated in the `1st July Workflow/` folder.

### Confusion matrix

<div align="center">

<img src="./1st%20July%20Workflow/confusion_matrix.png" alt="Confusion Matrix" width="47%">
<img src="./1st%20July%20Workflow/confusion_matrix_normalized.png" alt="Normalized Confusion Matrix" width="47%">

</div>

### Manual test confusion matrix

<div align="center">

<img src="./1st%20July%20Workflow/manual_test_confusion_matrix.png" alt="Manual Test Confusion Matrix" width="60%">

</div>

### Dataset label distribution

<div align="center">

<img src="./1st%20July%20Workflow/labels.jpg" alt="Label Distribution" width="60%">

</div>

### Training metric curves

<div align="center">

<img src="./1st%20July%20Workflow/metrics_mAP50(B).png" alt="mAP50" width="47%">
<img src="./1st%20July%20Workflow/metrics_mAP50-95(B).png" alt="mAP50-95" width="47%">

<br>

<img src="./1st%20July%20Workflow/metrics_precision(B).png" alt="Precision" width="47%">
<img src="./1st%20July%20Workflow/metrics_recall(B).png" alt="Recall" width="47%">

</div>

### Main output files

| File | Purpose |
|---|---|
| `best.pt` | Best validation checkpoint from training |
| `last.pt` | Final epoch checkpoint |
| `results.csv` | Epoch-by-epoch training metrics/losses |
| `confusion_matrix.png` | Ultralytics validation confusion matrix |
| `confusion_matrix_normalized.png` | Normalized validation confusion matrix |
| `manual_test_predictions.csv` | Manual object-level predictions on test split |
| `manual_test_confusion_matrix.csv` | CSV version of manual test confusion matrix |
| `manual_test_confusion_matrix.png` | Manual test confusion matrix plot |
| `manual_test_classification_report.csv` | Precision, recall, F1-score per class |
| `data.yaml` | Dataset class definition and split paths |

---

## 🎯 Project Overview

GRIP, short for **Glove Rejection and Inspection Process**, is a real-time quality inspection system for nitrile glove manufacturing conveyor lines. The system uses a top-down camera to inspect gloves moving on a conveyor and identifies gloves that should be rejected or sent for manual inspection.

The long-term objective is to integrate:

```text
Camera
↓
Computer vision model
↓
Decision logic
↓
SCARA robot
↓
Pneumatic/vacuum gripper
↓
Reject/manual inspection bin
```

The system is designed so that normal gloves continue on the belt, while wrong-hand gloves, label defects, and uncertain cases are removed or flagged without stopping the conveyor.

---

## ❗ Problem Being Solved

| Problem | Effect on Production |
|---|---|
| Left/right glove mix-up | Packaging errors and customer complaints |
| Label defects | Smudged, missing, or unclear printed information |
| Manual visual inspection | Fatigue, inconsistency, and missed defects |
| High belt speed | Difficult to inspect accurately by hand |
| No automatic defect logging | Hard to track defect rate and improve process |

The MVP focuses on **wrong-hand glove rejection** first. Label defect detection and size verification are planned future stages.

---

## 🏭 Factory MVP Scenario

The current factory setup has glove frames recorded from a moving conveyor belt.

```text
Expected flow: left gloves should pass
Wrong-hand case: right gloves should be rejected or sent to manual inspection
Unclear case: crumpled, blurred, folded, partial, or visually unsafe gloves should not be auto-passed
```

The project should not force a left/right decision when the glove is visually unsafe. The correct industrial behavior is:

```text
Clear LEFT  → PASS
Clear RIGHT → REJECT / PICK
UNCLEAR     → MANUAL / RECHECK
```

---

## 🧠 Computer Vision Development Timeline

### 1. Normal YOLO glove detection

Initial experiments trained YOLO-style models to detect gloves in factory frames.

**Outcome**

- Bounding box detection worked well.
- This remains useful as the base detection stage.

**Limitation**

- Detection alone does not decide whether the glove is left or right.

---

### 2. YOLO left/right object classification

Next, YOLO was trained with classes such as:

```text
left_glove
right_glove
unclear_glove
```

**Outcome**

- Validation metrics looked promising on prepared data.

**Failure mode**

- Generalisation to new mixed conveyor videos was not reliable enough.
- Issues included motion blur, camera-angle variation, partial gloves, and domain shift.

---

### 3. CNN crop classifier

A two-stage pipeline was tested:

```text
YOLO detects glove bbox
↓
crop glove
↓
CNN classifies left_glove / right_glove / unclear_glove
```

**Observed result**

- Around 86.7% test accuracy on a small split.

**Failure mode**

- Performance dropped on new mixed videos when crops were blurred, partial, or different from training examples.

**Conclusion**

- CNN crop classification is useful as a baseline, but not trusted as the final decision layer yet.

---

### 4. YOLO-pose/keypoint experiments

YOLO-pose was tested to detect glove keypoints and later use geometry-based logic.

**Observed issue**

- Gloves are not real hands.
- They are empty, deformable, folded, and crumpled.
- In many frames, only four fingers are visible or the thumb is hidden.
- Missing keypoints make pose unstable.

**Conclusion**

- Pose is not abandoned, but it should not be the only urgent solution.
- It may become useful later if a cleaner, consistently annotated keypoint dataset is created.

---

### 5. Latest urgent MVP: YOLO11n 3-class detect

The latest working experiment trains YOLO detection directly on:

```text
0 = left_glove
1 = right_glove
2 = unclear_glove
```

This uses full glove bounding boxes and avoids keypoint dependency for now.

**Why this was done**

- Time was limited.
- Annotating every keypoint in every frame was not practical.
- Many frames were visually unclear or crumpled.
- The system needed a quick testable `best.pt` model for mixed dataset/video evaluation.

---

## 🏗 Current Practical Pipeline

The current test pipeline is:

```text
Video / image input
↓
YOLO11n detect model: best.pt
↓
Detect left_glove / right_glove / unclear_glove
↓
Display bbox + class + confidence
↓
Check dangerous errors manually
↓
Save screenshots / logs for failure analysis
```

Recommended future robust pipeline:

```text
Camera
↓
YOLO glove detector
↓
Tracker, e.g. ByteTrack
↓
Crop each tracked glove
↓
Classify LEFT / RIGHT / UNCLEAR
↓
Reject threshold + temporal voting
↓
SCARA command queue
```

Pose/keypoints can be re-added only if keypoint predictions become stable enough.

---

## 🧪 Current Testing Scripts

### Important warning

Do not test the latest `best.pt` using the old pose tester.

If the window says:

```text
GRIP YOLO-Pose Test
This test checks pose quality only
```

then it is the wrong script for the latest detect model.

The latest model is a **detect** model. Use the detector tester script:

```text
grip_video_detect_tester.py
```

### Test latest `best.pt` on mixed video

```bash
python grip_video_detect_tester.py --model best.pt --source Mixed_Dataset_video.mp4 --conf 0.05 --imgsz 960
```

If detections are missing, test with lower confidence and larger image size:

```bash
python grip_video_detect_tester.py --model best.pt --source Mixed_Dataset_video.mp4 --conf 0.01 --imgsz 1280
```

### What the script should print

The correct model should show something like:

```text
model.task = detect
model.names = {0: 'left_glove', 1: 'right_glove', 2: 'unclear_glove'}
```

If it prints `pose`, then the wrong model was loaded.

---

## 📁 Latest Workflow Folder Structure

The GitHub folder for the latest experiment is:

```text
1st July Workflow/
├── best.pt
├── last.pt
├── data.yaml
├── results.csv
├── labels.jpg
├── confusion_matrix.png
├── confusion_matrix_normalized.png
├── manual_test_predictions.csv
├── manual_test_confusion_matrix.csv
├── manual_test_confusion_matrix.png
├── manual_test_classification_report.csv
├── metrics_mAP50(B).png
├── metrics_mAP50-95(B).png
├── metrics_precision(B).png
└── metrics_recall(B).png
```

---

## ✍️ Current Annotation Rules

### Class labeling rule

Label the image based on what is **visually decidable**, not only based on the source folder.

| Situation | Label |
|---|---|
| Clearly visible left glove | `left_glove` |
| Clearly visible right glove | `right_glove` |
| Crumpled, folded, blurred, partial, or visually unsafe glove | `unclear_glove` |
| Known left/right from folder but image itself is not clear | `unclear_glove` |

### Keypoint rule for future pose work

```text
Visible point      → mark it
Hidden but certain → mark as occluded only if truly confident
Unknown point      → do not guess
```

Bad keypoints are worse than missing keypoints.

---

## ✅ What Worked, What Failed

### What worked

| Attempt | Result |
|---|---|
| Factory video frame extraction | Worked |
| Custom annotation UI | Worked |
| 70/20/10 dataset split | Worked |
| YOLO-pose labels converted to YOLO detect format | Worked for urgent test |
| YOLO11n 3-class detect training in Colab | Worked |
| `best.pt` and result files generated | Worked |
| GitHub workflow folder uploaded | Worked |
| Video detector tester script prepared | Worked |

### What failed or needs improvement

| Issue | Problem |
|---|---|
| Pose/keypoints as immediate main method | Many gloves have hidden fingers/thumbs, making keypoint annotation and prediction unstable |
| Old pose tester used with detect model | Wrong script for latest `best.pt`; use detector tester instead |
| `unclear_glove` imbalance | The dataset has many more unclear/crumpled images than left/right images |
| Mixed video detection | Needs testing with the correct detector script and correct `best.pt` |
| Current camera quality | Motion blur and low clarity reduce model reliability |
| Small dataset | 608 total images is only a prototype-scale dataset |

---

## 🐛 Known Risks and Mitigations

| Risk | Why it matters | Mitigation |
|---|---|---|
| Right glove predicted as left | Dangerous false pass | Track this separately; tune threshold; add more right-glove samples |
| Model over-predicts unclear | Dataset imbalance | Balance train set; collect more left/right examples |
| Motion blur | Fingers/thumb become invisible | Better lighting, shorter exposure, global shutter camera, blur augmentation |
| Partial gloves at frame edges | Crops may be ambiguous | Use tracking and decide only near a stable decision line |
| Pose instability | Hidden anatomy breaks keypoint logic | Use pose only after more consistent annotation |
| Domain shift on mixed video | Training/test videos may differ | Collect independent validation clips and failure cases |

---

## 💻 Installation

```bash
# Clone repository
git clone https://github.com/LGsekara1/Glove_Rejection_and_Inspection_Process_-GRIP-.git
cd Glove_Rejection_and_Inspection_Process_-GRIP-

# Create virtual environment
py -3.11 -m venv grip_env

# Activate on Windows
grip_env\Scripts\activate

# Install PyTorch with CUDA
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121

# Install project dependencies
pip install ultralytics opencv-python numpy albumentations pillow tqdm pyyaml scikit-learn pyserial streamlit
```

Check GPU:

```bash
python -c "import torch; print(torch.cuda.is_available()); print(torch.cuda.get_device_name(0))"
```

---

## ▶️ Running the Latest Detect Model

Place these files in one testing folder:

```text
test_folder/
├── grip_video_detect_tester.py
├── best.pt
└── Mixed_Dataset_video.mp4
```

Run:

```bash
python grip_video_detect_tester.py --model best.pt --source Mixed_Dataset_video.mp4 --conf 0.05 --imgsz 960
```

Controls:

```text
q       quit
space   pause/resume
s       save screenshot
[       lower confidence threshold
]       increase confidence threshold
-       lower image size
=       increase image size
```

---

## 🤖 SCARA Integration Plan

Robot integration is not active yet. It will be added after the vision decision becomes reliable.

### Planned command format

```json
{
  "cmd": "pick",
  "track_id": 42,
  "decision": "REJECT",
  "reason": "wrong_hand_or_unclear",
  "bbox": [x1, y1, x2, y2],
  "confidence": 0.91,
  "timestamp": 1720100234.512
}
```

### Planned decisions

| Decision | Meaning |
|---|---|
| `PASS` | Correct left glove |
| `REJECT` | Confident right glove |
| `MANUAL` | Unclear/folded/low-confidence glove |
| `IGNORE` | False detection or too low confidence |

### Pick timing formula

```text
pick_delay_seconds = camera_to_scara_distance_mm / conveyor_speed_mm_s
trigger_pulse = detection_encoder_pulse + distance_pulses + latency_offset
```

The timestamp-based version can be used for early tests. Encoder-based timing is required for final precision.

---

## 🚀 Next Steps

### Immediate next steps

1. Run the latest `best.pt` with `grip_video_detect_tester.py` on the mixed video dataset.
2. Confirm the terminal prints:

```text
model.task = detect
model.names = {0: 'left_glove', 1: 'right_glove', 2: 'unclear_glove'}
```

3. Save screenshots of false predictions.
4. Check how many true right gloves are predicted as left.
5. Add more real right and left examples from the same mixed-video setup.
6. Retrain with a cleaner, more balanced dataset.

### Medium-term steps

- Add temporal voting across 5-10 frames per tracked glove.
- Add a tracker to prevent duplicate robot commands.
- Improve lighting and camera exposure.
- Re-test pose only after building a more consistent keypoint dataset.
- Add label defect detection later as a separate stage.

### Long-term improvements

- Jetson Orin Nano deployment.
- TensorRT export.
- Multi-lane operation.
- Size classification.
- Label ROI inspection.
- SCARA pick-and-place integration.
- Failure-case logging dashboard.

---

## 📚 References / Project Materials

- Ultralytics YOLO Detection Documentation
- Ultralytics YOLO Pose Documentation
- GRIP Project Proposal and Mid-Evaluation Materials
- Factory video experiments and failure-case logs
- 1st July YOLO Detect Workflow results

---

<div align="center">

**Group MOSFET · ENTC, University of Moratuwa · 2026**

*GRIP - Glove Rejection and Inspection Process*

</div>
