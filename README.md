# FEB-Bench

FEB-Bench (Fiber End-face Boundary Detection Benchmark) is a small-scale dataset for optical fiber end-face boundary detection and geometric metrology.

The dataset contains 535 annotated samples covering two imaging domains:

- side-illumination
- coaxial-reflection microscopy

and three representative end-face structures:

- single-mode
- octagon
- four-core

The annotations are measurement-consistent single-pixel boundary maps used for downstream geometric measurement.

## Dataset Structure

```text
FEB-Bench/
├── train/
│   ├── images/
│   └── edges/
└── val/
    ├── images/
    └── edges/
