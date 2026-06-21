# Technical Reference: AI-Driven Visual Inspection for High-Precision PCB/PCBA Manufacturing

## 1. AI Model Selection per Defect Type

In high-throughput Electronics Manufacturing Services (EMS), selecting the optimal model architecture is a function of defect morphology and the required inspection resolution. The following mapping leverages state-of-the-art benchmarks for the 12 critical defect classes identified in high-density PCBA production.

### Defect-to-Architecture Mapping Table

| Defect Type | Recommended Model Family | Performance Metrics (Precision/Recall/mAP/FPS) | Technical Justification |
| :--- | :--- | :--- | :--- |
| **Missing Component / Wrong Component** | **Improved YOLOv11** | 93.1% Precision / 84.6% Recall | Integration of **MD-C2F** (point-moving feature extraction) enables precise identification in complex backgrounds. |
| **Resistor Defects** (Small targets) | **Improved YOLOv11** | 89.3% Precision / 166 FPS | Employs **DualConv** shared convolution and **DCNv4** (Deformable Convolutional Network) to capture fine-grained features. |
| **LED / Capacitor Defects** | **Improved YOLOv11** | 77.8% mAP50 (LED) / 85.3% mAP50 (Capacitor) | Superior to YOLOv7 (27.4% mAP) due to **Inner_MPDIoU** loss, which accelerates bounding box regression for irregular shapes. |
| **Polarity Reversal / Misalignment** | **Improved YOLOv11** | 93.2% Precision / 166 FPS | Multi-scale feature fusion via the **Neck** architecture handles subtle shifts and rotation variances. |
| **Tombstoning / Billboarding** | **3D-AOI + YOLOv11** | High Sensitivity (3D Depth) | 2D systems fail on height-based defects; 3D point-cloud data provides the volumetric context for height-based detection. |
| **Wrong Marking (OCR)** | **Improved YOLOv11** | 93.1% Precision | Enhanced feature map representation through **C2PSA** (Parallel Spatial Attention) modules to focus on textual markings. |
| **Solder Bridge / Short** | **Y-MaskNet** (YOLOv5 + Mask R-CNN) | 0.72 mAP@[0.5:0.95] | Combines YOLOv5 speed with Mask R-CNN pixel-level masks to delineate bridge boundaries. |
| **Insufficient / Cold Solder** | **Y-MaskNet / 3D-AOI** | 7% IoU Improvement over baseline | **RoIAlign** eliminates quantization errors, critical for the fine-grained segmentation of solder fillets. |
| **Solder Void (BGA/Hidden)** | **U-Net** (AXI/X-Ray) | Pixel-level Segmentation | Volumetric segmentation is required to identify air gaps within internal solder joints hidden from optical view. |
| **Lifted Lead** | **3D-AOI / Y-MaskNet** | High Volumetric Accuracy | Requires height-sensitive geometry measurement rather than surface appearance — **out of single-camera 2D scope; flag it**. |
| **Bent / Missing Pins** | **Improved YOLOv11** | 95.4% mAP50 (PKU benchmark) | **Inner_MPDIoU** dynamically adjusts auxiliary bounding boxes to capture tiny, high-aspect-ratio pin defects. |
| **General Classification** | **ResNet50 + t-SNE + K-means** | 97.33% Accuracy | Hybrid approach using transfer learning and dimensionality reduction for high-precision categorization in approx. 60s. |

> **Read the table as "if a supervised detector is warranted, which family,"** not
> as a mandate to use YOLO everywhere. In this product `yolo` is one detector the
> sibling service exposes — we *consume* it, we don't tune its internals (DualConv /
> MD-C2F / DCNv4 etc. are upstream architecture trivia, not our knobs). For most
> components the cheaper `template_match` or `anomalib` path is the right first
> choice — see the project-reality reference for why supervised YOLO is the
> exception, not the default, in a golden-sample / scarce-label flow.

---

## 2. Supervised Learning vs. Unsupervised Anomaly Detection

Industrial PCB lines require a bifurcated strategy: supervised models for known, repeating defects and unsupervised models for novel anomalies or "golden board" verification where defect data is scarce.

### Detection Paradigm Benchmark

| Feature | Improved YOLOv11 | PatchCore (Unsupervised) | Y-MaskNet (Segmentation) |
| :--- | :--- | :--- | :--- |
| **Paradigm** | Fully Supervised | Self-Supervised (SSL) | Multi-task Joint Learning |
| **Data Requirement** | Labeled defect instances | Only "good" (defect-free) samples | Labeled masks + boxes |
| **Latency (FPS)** | **166 FPS (Std) / 222 FPS (Opt)** | ~30-50 FPS (Tile-dependent) | < 60 FPS |
| **Primary Use Case** | Known defect localization (e.g., Short) | Counterfeit / Novelty Detection | Fine-grained crack/joint segmentation |
| **Precision** | 89.3% (Resistors) | High (Context-dependent) | 0.89 (PCB Defect Dataset) |

### Implementation Nuance: SSL and Tiling
For **PatchCore** and **FastFlow** architectures, consistent lighting and framing are prerequisites. To handle high-resolution PCB imagery, we implement a **"Slicing and Stitching"** strategy, dividing the board into 512x512 pixel tiles. This ensures local anomalies (e.g., a missing 0201 capacitor) are not "washed out" in the global feature vector. We recommend fine-tuning the **ResNet** backbone using **Self-Supervised Learning (SSL)** on the specific production line data to adapt embeddings to unique solder paste reflectivity and board masking colors.

---

## 3. Inspection Modality Decision Rules: AOI vs. 3D-AOI vs. X-Ray

A logic-based trigger system is essential to dictate hardware-software transitions based on joint accessibility and defect geometry.

> **This project is single-camera 2D optical only (v1).** Rule 1 is in scope.
> Rules 2–3 are the honest limits — say them plainly and propose the 2D proxy
> (anomaly-on-good, multi-angle lighting) rather than over-promising; do not
> claim a 3D/X-ray capability the golden-sample editor does not have.

*   **RULE 1 (IN SCOPE): IF [Surface Defect] -> 2D AOI (`template_match` / `anomalib` / `yolo`).**
    *   *Scope:* missing/wrong component, marking, orientation, misalignment, surface shorts. Limited by metal reflection + component overlap.
*   **RULE 2 (OUT OF 2D SCOPE — flag): IF [Height/Volume defect] -> needs 3D-AOI.**
    *   *Scope:* tombstoning, billboarding, lifted lead, solder volume. A single 2D camera cannot measure z-height — flag the BOM item and fall back to anomaly-on-good, never promise a true 3D call.
*   **RULE 3 (OUT OF SCOPE — flag): IF [Hidden joint: BGA/QFN/internal] -> needs X-ray (AXI).**
    *   *Scope:* BGA voids, via fill, internal shorts. Optically invisible — flag when the BOM carries BGA/QFN and route to a separate AXI process.

---

## 4. Training Strategy & Industrial Dataset Management

### Dataset Catalog & Resolution Requirements
Training high-accuracy models (mAP50 > 95%) requires multi-source data integration:
*   **DeepPCB:** 5,000+ images (256x256 resolution). Best for crack and solder joint fine-feature training.
*   **PCB Defect Dataset:** 7,000+ images (512x512 resolution). Standard for short/open circuit localization.
*   **PKU-Market-PCB:** 1,386 images across 6 defect types (Missing hole, Mouse bite, Open, Short, Spur, Spurious copper).

### Mitigation of Data Imbalance
In healthy production, defects represent **< 1%** of samples. To prevent model bias:
1.  **Synthetic Generation:** Follow the Meng et al. strategy—randomly place electronic component types and labels on bare boards. In industrial setups, we augment 400 real images with **1,000 synthetic images** to ensure class balance.
2.  **Loss Optimization:** Implement **Focal Loss** or **Inner_MPDIoU**. The latter uses a scale factor to modify the auxiliary bounding box, accelerating convergence for targets with high shape variation (e.g., "mouse bites").
3.  **Confusion Matrix Monitoring:** Specific attention must be paid to the **Resistor/Pad morphology** confusion; high-density layouts often trigger false positives where pads are identified as resistors.

### Deployment notes (project-relevant only)
*   **Quantization re-validation:** if weights are quantized (INT8/FP16, e.g. TensorRT) for edge inference, **re-validate against acceptance thresholds** afterward — quantization noise can mask subtle defects. This is the only quantization concern that survives into this product.
*   **Drift retrain trigger:** re-validate + retrain on a **new board revision or component-vendor change** — those shift the "good" distribution and silently raise the escape rate.

> Cloud-MLOps infrastructure (Kubernetes, IAM/VPC, ISO-27001, SageMaker/MLflow)
> is **out of scope** here: the editor is a FastAPI + Postgres app that drives the
> sibling `auto-inspect-service` over HTTP. Don't recommend that stack.