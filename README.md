# Edge Health MVP: offline clinical-image quality gate

## Research question

Can a low-cost device reject images that are too dark, too bright, too blurry, or too small **before** they are submitted to a clinician or an independently validated triage model?

The MVP runs fully offline and does not classify diseases. Its purpose is to prevent unsafe overconfidence: an image that fails quality checks is marked `RECAPTURE_REQUIRED` rather than being passed to a medical model.

## Why this is the correct MVP boundary

Deploying a skin-lesion classifier trained on public images as a clinical tool in Costa Rica would be unsafe without local validation, a clinical partner, approval, and medical-device review. Image quality is a measurable engineering contribution that can be built and evaluated now without collecting patient data.

## What runs

`src/quality_gate.py` loads an image locally and calculates:

- resolution;
- normalized brightness;
- normalized contrast;
- a simple focus proxy from adjacent-pixel gradients.

It returns structured JSON with a `quality_status`, explicit rejection reasons, and the measurements. Thresholds are placeholders for experimentation, not clinical cutoffs.

## Dataset and research-model scaffold

The repository includes a reproducible **research-only** MobileNetV3 baseline trainer in `training/train_baseline.py`. It is designed for the public PAD-UFES-20 smartphone-image dataset and uses a patient-level split to avoid leakage. It downloads ImageNet weights only when run locally, then trains a local checkpoint; no disease-classification model artifact is committed or presented as clinically safe.

See [the data and model plan](docs/dataset-and-model-plan.md) before downloading data or training.

## Run

The script needs only Python 3, NumPy, and Pillow.

```bash
python3 src/quality_gate.py /absolute/path/to/example.jpg
python3 -m unittest discover -s tests -v
```

## Path to a credible Edge-AI study

1. Partner with a dermatology/primary-care mentor before obtaining any clinical image.
2. Agree on a non-diagnostic triage endpoint and an IRB/ethics pathway.
3. Benchmark a legally usable, validated baseline such as MobileNetV3 on public data only.
4. Compare FP32, FP16, and INT8 on three named Android devices: latency, model size, memory, battery use, and failure rate.
5. Measure quality-gate false rejection and acceptance using clinician-labelled image quality.
6. Only then evaluate any clinical performance by subgroup and report uncertainty.

## Safety statement

This repository must not be used to make diagnostic, treatment, referral, or emergency decisions. It contains no trained disease model and stores no patient data.
