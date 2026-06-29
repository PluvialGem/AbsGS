# README_IMPROVEMENT

## 1. Overview

This repository is forked from the official AbsGS implementation. All modifications are implemented on the `improvement` branch. The project reproduces the original AbsGS baseline and evaluates two methodological improvements:

1. **Edge-Aware Residual Loss**: adds an image-space Sobel-edge weighted residual loss.
2. **Visibility-Opacity Guided Pruning (VOP)**: records Gaussian visibility during training and conservatively prunes low-visibility low-opacity Gaussians.

These changes are methodological code modifications, not pure hyperparameter tuning or dataset substitution.

## 2. Environment

- OS: Windows 11 64-bit
- GPU: NVIDIA GeForce RTX 4060 Laptop GPU, 8GB VRAM
- Python: 3.10
- PyTorch: 2.1.2+cu118
- CUDA Toolkit: 11.8
- Repository path: `E:\SLAM2026Final\code\AbsGS`
- Conda environment: `E:\SLAM2026Final\envs\slam_abs`

## 3. Datasets

- Scene 1: NeRF Synthetic `lego`
- Scene 2: Tanks & Temples `truck`

Data paths:

```bat
E:\SLAM2026Final\data\nerf_synthetic\lego
E:\SLAM2026Final\data\tandt_db\tandt\truck
```

## 4. Modified Files

```text
utils/loss_utils.py           # Sobel edge map and edge-aware residual loss
arguments/__init__.py         # New command-line switches
train.py                      # Loss integration and VOP scheduling
scene/gaussian_model.py       # Visibility statistics and pruning rule
```

## 5. Baseline Commands

### lego baseline

```bat
python train.py -s E:\SLAM2026Final\data\nerf_synthetic\lego -m E:\SLAM2026Final\outputs\exp\lego_baseline --eval --white_background -r 1 --iterations 30000 --test_iterations -1 --save_iterations 30000
python render.py -s E:\SLAM2026Final\data\nerf_synthetic\lego -m E:\SLAM2026Final\outputs\exp\lego_baseline --iteration 30000 --skip_train
python metrics.py -m E:\SLAM2026Final\outputs\exp\lego_baseline
```

### truck baseline

```bat
python train.py -s E:\SLAM2026Final\data\tandt_db\tandt\truck -m E:\SLAM2026Final\outputs\exp\truck_baseline --eval -r 2 --iterations 30000 --test_iterations -1 --save_iterations 30000
python render.py -s E:\SLAM2026Final\data\tandt_db\tandt\truck -m E:\SLAM2026Final\outputs\exp\truck_baseline --iteration 30000 --skip_train
python metrics.py -m E:\SLAM2026Final\outputs\exp\truck_baseline
```

## 6. Improved Commands

### Edge Loss only

```bat
python train.py -s E:\SLAM2026Final\data\nerf_synthetic\lego -m E:\SLAM2026Final\outputs\exp\lego_edge --eval --white_background -r 1 --iterations 30000 --test_iterations -1 --save_iterations 30000 --use_edge_loss --edge_loss_weight 0.05
python train.py -s E:\SLAM2026Final\data\tandt_db\tandt\truck -m E:\SLAM2026Final\outputs\exp\truck_edge --eval -r 2 --iterations 30000 --test_iterations -1 --save_iterations 30000 --use_edge_loss --edge_loss_weight 0.05
```

### VOP only

```bat
python train.py -s E:\SLAM2026Final\data\nerf_synthetic\lego -m E:\SLAM2026Final\outputs\exp\lego_vop --eval --white_background -r 1 --iterations 30000 --test_iterations -1 --save_iterations 30000 --use_visibility_pruning
python train.py -s E:\SLAM2026Final\data\tandt_db\tandt\truck -m E:\SLAM2026Final\outputs\exp\truck_vop --eval -r 2 --iterations 30000 --test_iterations -1 --save_iterations 30000 --use_visibility_pruning
```

### Edge Loss + VOP

```bat
python train.py -s E:\SLAM2026Final\data\nerf_synthetic\lego -m E:\SLAM2026Final\outputs\exp\lego_edge_vop --eval --white_background -r 1 --iterations 30000 --test_iterations -1 --save_iterations 30000 --use_edge_loss --edge_loss_weight 0.05 --use_visibility_pruning
python train.py -s E:\SLAM2026Final\data\tandt_db\tandt\truck -m E:\SLAM2026Final\outputs\exp\truck_edge_vop --eval -r 2 --iterations 30000 --test_iterations -1 --save_iterations 30000 --use_edge_loss --edge_loss_weight 0.05 --use_visibility_pruning
```

## 7. Evaluation

```bat
python render.py -s <DATASET_PATH> -m <MODEL_PATH> --iteration 30000 --skip_train
python metrics.py -m <MODEL_PATH>
```

## 8. Logs and Results

```text
E:\SLAM2026Final\logs\improve
E:\SLAM2026Final\outputs\exp
E:\SLAM2026Final\metrics\improve\final_experiment_table.csv
E:\SLAM2026Final\figures\improve
```

## 9. Reproducibility Notes

All baseline and improved methods use the same two scenes, same GPU, same iteration count, same resolution setting, and same evaluation scripts. This ensures fair comparison.

## 10. AI Usage Statement

AI tools were used to assist with environment setup, debugging, code-editing guidance, command organization, and report drafting. All experiments and metrics were generated from local runs.
