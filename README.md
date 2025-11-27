# Crater-Detection-by-RT-DETR

🌙 RT-DETR Sub–Kilometre Lunar Crater Detection

Transformer-based crater detection trained on WAC imagery and evaluated on CH-2 OHRC
Model trained for 100 epochs for ~50 hours on NVIDIA A100 (80GB).

This repository contains the RT-DETR implementation used to detect and analyze lunar impact craters < 1 km diameter.
The model was trained on a custom dataset derived from LROC WAC (~100 m/pixel) and evaluated on high-resolution Chandrayaan-2 OHRC (0.25 m/pixel) scenes.

🎯 Research Objectives

Train a transformer-based detector on low-resolution global imagery (WAC).

Test whether it generalises to high-resolution OHRC scenes.

Study the influence of crater morphology, metadata quality, and label noise.

Compare RT-DETR performance with YOLO-based baselines.

Unlike Earth-based CV, lunar imagery is harsh:

Weak texture cues,

Low contrast,

Degraded morphology,

Geological ambiguity.

Transformers are promising because they:

Model global spatial context — crater rim → slope → shadow → ejecta.

📁 Repository Contents
RT-DETR-Subkm-Craters
├── weights/             # trained RT-DETR weights (best.pt / last.pt)
├── runs/                # predictions + training logs
│   ├── train/           # loss curves, mAP, batch images
│   └── detect/          # inference on OHRC tiles
├── metrics/             # PR-F1 plots, confusion, IoU metrics
├── config/data.yaml     # dataset spec (single class: crater)
├── scripts/
│   ├── train_rtdetr.py        # training
│   ├── inference.py           # inference on tiles
│   ├── make_tiles_wac.py      # WAC → 256×256 tiles
│   ├── make_tiles_ohrc.py     # OHRC → 4096×4096 tiles
│   ├── generate_labels.py     # crater catalog → YOLO labels
│   ├── overlay_craters.py     # ground truth sanity checks
│   └── parse_pds4.py          # OHRC .IMG + .XML → GeoTIFF
└── README.md
All code, results, weights, configs, and scripts are included.
Only raw imagery is excluded (NASA/ISRO policy).

🛰️ Dataset Summary (Not Included)
Training Data

Source: LROC WAC mosaic

Resolution: 100 m/pixel

Tiles: 60,506

Train: 48,404

Val: 12,102

Tile size: 256×256

Labels

Crater centers + diameters from LU5M812TGT catalog
Converted from (lat, lon, km) → YOLO bounding boxes.

Testing Data

Source: Chandrayaan-2 OHRC PDS4

Resolution: 0.25 m/pixel

Tile size: 4096×4096

Post filtering: 725 crater tiles

The entire dataset was built by hand — not downloaded.

🌐 Lunar CRS & Geometry

Moon radius:

𝑅=1,737,400𝑚
R=1,737,400m

Circumference:

𝐶=2𝜋𝑅≈10,921,840𝑚
C=2πR≈10,921,840m

Angular conversion:

1∘≈30.338𝑘𝑚
1∘≈30.338km

📌 Custom Geodetic CRS (WKT)

Used to convert OHRC .IMG → georeferenced GeoTIFF:

GEOGCS["GCS_Moon_2000",
  DATUM["D_Moon_2000",
    SPHEROID["Moon_2000_IAU_IAG",1737400.0,0.0]],
  PRIMEM["Reference_Meridian",0.0],
  UNIT["Degree",0.0174532925199433]]
🧠 Model: RT-DETR

CNN backbone (feature extractor)

Transformer decoder (object queries)

End-to-end predictions

No anchors, no post-processing heuristics

Why this matters:

Craters are not texture blobs — they are geometric structures.

Transformers

learn rim gradient → interior bowl → shadow halo relationship,

instead of relying on local contrast.

🔥 Training Configuration

| Parameter     | Value                   |
| ------------- | ----------------------- |
| Model         | RT-DETR                 |
| Dataset       | WAC tiles               |
| Epochs        | **100**                 |
| GPU           | **NVIDIA A100 — 80 GB** |
| Resolution    | 256×256                 |
| Training Time | **≈ 50 hours**          |
| Batch Size    | GPU-dependent           |

📊 Final Metrics (Epoch 100)

| Metric       | Value       |
| ------------ | ----------- |
| Precision    | **0.65622** |
| Recall       | **0.44973** |
| F1 Score     | **0.10301** |
| mAP@0.5      | **0.77437** |
| mAP@0.5–0.95 | **0.33663** |

Interpretation

Strong morphological detection (high mAP@0.5)

Weak completeness (low recall)

Underfitting of rim edges (low mAP@0.5–0.95)

Transformers typically stabilise at >200 epochs.

🧩 Known Limitations

High training cost

Slow convergence vs YOLO

Sensitive to metadata shifts

Misses shallow degraded craters

Catalog near resolution limit introduces label noise

🔬 Research Insights
RT-DETR outperforms YOLO on:

✔ low false positives
✔ crater semantic consistency
✔ global morphology

YOLO outperforms on:

✔ recall
✔ localisation precision
✔ small degraded rim detection

Together:

attention + anchor-based models offer complementary strengths.

📎 Reproducibility

Everything required to recreate the dataset pipeline exists in /scripts:

WAC tiling

OHRC tiling

label generation

CRS conversion

overlay sanity checks

You only need raw mission data.

📚 Citation

If you use this work:

Gandepalli, D. “Sub-Kilometre Lunar Crater Detection using RT-DETR and Multi-Resolution Lunar Imagery.”
M.Tech Thesis, IIT Bhilai, 2025.

🙏 Acknowledgements

MANAS Lab, IIT Bhilai — compute resources (A100 80GB GPU)

ISRO Chandrayaan-2 — OHRC/TMC imagery

NASA LROC mission — WAC mosaics

Supervisor: Dr. Nitin Khanna

Author

GANDEPALLI DHANUSH
M.Tech — Control & Instrumentation
Indian Institute of Technology Bhilai
