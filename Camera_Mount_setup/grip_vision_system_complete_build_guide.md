# GRIP Vision System — Complete Build Guide
### Camera Mount, Lighting, Clamping & Conveyor Vision Setup

Prepared for: Team MOSFET  
Project: GRIP — Glove Rejection and Inspection Process  
University of Moratuwa

---

# 1. SYSTEM OVERVIEW

This document contains the complete hardware list, 3D printed parts, mechanical dimensions, assembly instructions, lighting recommendations, and learning resources required to build the GRIP vision inspection system.

The setup is designed for:

- 1 meter conveyor belt
- Two glove lanes
- Single-lane inspection initially
- Hikvision DS-U02 1080p camera
- YOLO-based glove inspection
- Future Jetson Nano integration

---

# 2. SYSTEM CONCEPT

The conveyor contains two glove lanes.

Initially, only one lane is inspected.

A rigid aluminum extrusion frame holds:

- Camera
- Lighting
- Diffuser system
- Cable routing

The camera is positioned directly above the conveyor for a top-down inspection view.

The structure is clamped onto the conveyor body using industrial-style clamps.

---

# 3. COMPLETE HARDWARE LIST

## 3.1 Aluminum Extrusion Cut List

| Part | Profile | Length | Qty |
|---|---|---|---|
| Vertical Column | 2040 | 700 mm | 1 |
| Horizontal Arm | 2040 | 800 mm | 1 |
| Base Support Left | 2020 | 350 mm | 1 |
| Base Support Right | 2020 | 350 mm | 1 |
| Bottom Cross Member | 2020 | 250 mm | 1 |

---

## 3.2 Fasteners & Connectors

| Item | Specification | Qty |
|---|---|---|
| M5 T-Nuts | Slot 6 | 30 |
| M5x8 Screws | Hex Socket | 30 |
| Corner Brackets | 2020/2040 compatible | 8 |
| Joining Plates | Flat Type | 4 |
| Rubber Feet | M5 | 4 |
| End Caps | 2020/2040 | 6 |

---

## 3.3 Camera System

| Item | Qty |
|---|---|
| Hikvision DS-U02 Camera | 1 |
| USB 3.0 Extension Cable (2m) | 1 |
| USB Ferrite Filter | 1 |

---

## 3.4 Lighting System

| Item | Specification | Qty |
|---|---|---|
| LED Bar Light | 6500K White | 2 |
| Diffuser Acrylic Sheet | Frosted White | 2 |
| 12V 2A Power Supply | SMPS | 1 |
| LED Dimmer | PWM 12V | 1 |

---

## 3.5 Clamping System

### Option A — Heavy Duty C-Clamp

| Item | Qty |
|---|---|
| Heavy Duty C-Clamp | 2 |
| Rubber Pad Sheet | 2 |
| Aluminum Plate 5mm | 2 |

---

### Option B — Industrial U-Bolt Clamp

| Item | Qty |
|---|---|
| U-Bolt Clamp Set | 2 |
| Aluminum L Plate | 2 |

---

## 3.6 Cable Management

| Item | Qty |
|---|---|
| Spiral Cable Wrap | 1 |
| Cable Ties | 1 pack |
| Cable Clips | 10 |

---

# 4. 3D PRINTED PARTS

Recommended Material:

- PETG preferred
- PLA+ acceptable for prototype

Recommended Settings:

- Layer Height: 0.2 mm
- Infill: 40–50%
- Walls: 4+

---

## 4.1 Print List

| Part | Qty |
|---|---|
| Camera Mount Holder | 1 |
| Tilt Adjustment Joint | 1 |
| Thumb Knob | 1 |
| LED Holder Brackets | 4 |
| Camera Hood | 1 |
| Cable Clips | 6 |
| Diffuser Holders | 4 |
| T-slot Spacer Blocks | 4 |

---

# 5. RECOMMENDED DIMENSIONS

## Camera Height

400–450 mm above conveyor

---

## Inspection Width

350–450 mm

(one glove lane only)

---

## Lighting Angle

45° from both sides

---

## Conveyor Width

1000 mm

---

# 6. IMPORTANT DESIGN RECOMMENDATIONS

## 6.1 Use Matte Black Surfaces

Avoid reflective shiny surfaces near the camera.

Reflections reduce segmentation quality and detection accuracy.

---

## 6.2 Use Controlled Lighting

Install:

- Side LED bars
- Diffusers
- Matte black side walls

This prevents:

- Shadows
- Glare
- Ambient light variations
- Reflection artifacts

---

## 6.3 Add Camera Hood

A hood around the camera helps block:

- Factory lights
- Sunlight
- Reflection

This greatly improves consistency.

---

## 6.4 Use Rigid Structure Only

Do NOT use:

- Flexible arms
- Ring light stands
- Microphone arms

The conveyor vibration will affect image quality.

---

## 6.5 Lock Camera After Calibration

Once the camera is calibrated:

- Tighten all screws
- Lock tilt mechanism
- Avoid moving camera again

Even small movements affect:

- Scale
- Perspective
- Detection consistency

---

# 7. BUILD INSTRUCTIONS

# STEP 1 — Cut Aluminum Extrusions

Cut the following:

- 700 mm vertical extrusion
- 800 mm horizontal extrusion
- Base supports

Important:

- Keep cuts square
- Deburr edges
- Verify dimensions before assembly

---

# STEP 2 — Assemble Base

Build:

- Left support
- Right support
- Bottom cross member

Install:

- Rubber feet
- Corner brackets

Ensure:

- Zero wobble
- Flat structure

---

# STEP 3 — Install Vertical Column

Use:

- Minimum 2 corner brackets
- Joining plate if necessary

Ensure the vertical column is perfectly perpendicular.

---

# STEP 4 — Install Horizontal Arm

Attach the 800 mm arm using:

- T-nuts
- M5 screws
- Corner brackets
- Joining plates

This section must be rigid.

---

# STEP 5 — Install Camera Mount

Attach:

- 3D printed camera holder
- Tilt mechanism
- Thumb knob
- Camera hood

Then mount:

- Hikvision DS-U02 camera

---

# STEP 6 — Install Lighting

Mount LED bars:

- 45° from both sides
- Equal height
- Equal distance from conveyor

Install:

- Diffuser acrylic sheets

---

# STEP 7 — Clamp Structure to Conveyor

Install:

- Rubber pads
- Clamp brackets
- C-clamps or U-bolts

Do NOT overtighten.

The mount should:

- remain rigid
- avoid deforming conveyor frame

---

# STEP 8 — Cable Management

Route:

- USB cable
- LED power cables

Secure using:

- Cable clips
- Spiral wrap
- Zip ties

Avoid hanging cables.

---

# 8. CAMERA POSITIONING GUIDE

## Recommended Orientation

Top-down view.

Avoid angled camera positions.

Benefits:

- Easier segmentation
- Easier left/right classification
- More consistent dataset
- Reduced perspective distortion

---

## Recommended Coverage

The glove should occupy:

- approximately 70% of image width

This improves:

- model accuracy
- feature visibility
- segmentation quality

---

# 9. LIGHTING ENCLOSURE DESIGN

Recommended structure:

- U-shaped tunnel
- Matte black side walls
- LED strips at 45°
- Diffuser acrylic

Purpose:

- Controlled illumination
- Reduced glare
- Better consistency

---

# 10. TOOLS REQUIRED

| Tool | Required |
|---|---|
| Allen Key Set | Yes |
| Metal Cutting Saw | Yes |
| Drill Machine | Optional |
| Vernier Caliper | Recommended |
| Miter Box | Helpful |
| Sandpaper/File | Yes |

---

# 11. WHERE TO BUY IN SRI LANKA

Possible suppliers:

- Tronic.lk
- Hexanode
- DuinoLK
- Daraz Sri Lanka

For aluminum extrusions:

- CNC shops
- Industrial automation suppliers
- 3D printer suppliers
- Pettah / Panchikawatte hardware suppliers

---

# 12. LEARNING RESOURCES

## Aluminum Extrusion Basics

Search:

"2020 aluminum extrusion beginner guide"

Recommended topics:

- extrusion assembly
- T-slot systems
- frame alignment

---

## T-Nuts & Corner Brackets

Search:

"How to use T-slot nuts aluminum extrusion"

Learn:

- sliding T-nuts
- tightening sequence
- alignment methods

---

## Cutting Aluminum Extrusions

Search:

"How to cut aluminum extrusion straight"

Recommended tools:

- miter saw
- hacksaw + miter box

---

## Conveyor Clamp Designs

Search:

"Industrial camera clamp conveyor"

Learn:

- anti-vibration mounting
- clamp positioning
- rigid support methods

---

## Machine Vision Lighting

Search:

"Machine vision lighting basics"

This is one of the most important areas.

Learn:

- glare reduction
- diffuser placement
- contrast optimization
- shadow elimination

---

## Industrial Camera Mounting

Search:

"Industrial camera mounting machine vision"

Learn:

- rigid mounting
- vibration isolation
- alignment methods

---

## Functional 3D Printing

Search:

"PETG functional print settings"

Learn:

- strong mechanical prints
- layer orientation
- infill selection

---

# 13. FINAL RECOMMENDATIONS

Priority order for development:

1. Build rigid structure
2. Build lighting enclosure
3. Collect consistent dataset
4. Train YOLO model
5. Optimize mechanics later

Lighting consistency affects detection quality more than camera quality.

A stable mount and controlled illumination are critical for reliable computer vision performance.

---

# END OF DOCUMENT


