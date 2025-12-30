# Emotion-Conditioned Text-to-Motion for 3D Human Movement

This repository accompanies the paper **“A Variational Text-to-Motion Model for Emotion-Aware 3D Human Movement”**.  
It contains the full pipeline for generating 3D human motion from short emotion-laden text prompts, using monocular videos recorded with volunteer actors.

The pipeline has three main stages:

1. **Monocular 3D pose estimation** from raw videos (HybrIK).
2. **Motion–language representation and metrics** (HumanML3D-style toolkit).
3. **Text-to-motion generation** with a variational model adapted from Text2Motion, fine-tuned on the **Emotion-based-dataset**.

---

## Repository structure

- **`Emotion-based-dataset/`**  
  Curated dataset of 923 monocular clips (videos + processed 22-joint skeletons) and free-form text prompts for six Ekman emotions (anger, disgust, fear, happiness, sadness, surprise).  
  Used as the training and evaluation data for all models in this repo.

- **`HybrIK/`**  
  Monocular 3D human pose estimation component (SMPL-based).  
  Used to lift raw RGB frames from `Emotion-based-dataset` into temporally consistent 3D pose sequences.  
  This folder is based on the official HybrIK implementation, with configuration scripts for this dataset.

- **`HumanML3D/`**  
  Code adapted from the HumanML3D / Text2Motion toolkit for:
  - motion–language feature extraction,
  - R-Precision, FID, diversity, and multimodality metrics,
  - baseline Text2Motion training on the 22-joint representation.

- **`text-to-motion/`**  
  Main VAE-based text-to-motion model for emotion-aware generation:
  - training scripts and configs for the proposed “Emotions-0.01” variant,
  - ablations (different KLD weights, with/without Text2Length and feature-alignment losses),
  - optional lightweight diffusion-style baseline for comparison.

- **`_Demo/`**  
  Example visualisations and scripts:
  - skeleton-based renderings of generated motions,
  - side-by-side comparisons with ground truth and baselines,
  - minimal inference demo that loads a trained checkpoint and generates motion from a text prompt.

---

## Getting started

1. **Environment**

   Create a conda or virtualenv with Python ≥ 3.8 and install PyTorch with CUDA support.  
   Then install the remaining dependencies (run from the repo root):

   ```bash
   pip install -r HumanML3D/requirements.txt
   pip install -r text-to-motion/requirements.txt
   ```

2. **Data preparation**

   - Download / place the processed dataset under `Emotion-based-dataset/`  
     (skeletons, text prompts, and split files as used in the paper).
   - To re-run pose estimation from raw videos, use the scripts in `HybrIK/` to export 22-joint sequences.

3. **Training the emotion-aware model**

   ```bash
   cd text-to-motion
   python train_emotions_vae.py --config configs/emotions_vae_kld_0_01.yaml
   ```

4. **Evaluation and visualisation**

   - Use `HumanML3D/eval_metrics.py` to compute R-Precision, FID, diversity, and multimodality.  
   - Run notebooks / scripts in `_Demo/` to render qualitative examples.

---

## References

Please also cite the upstream models this repository builds upon:

- **Text-to-motion / HumanML3D toolkit**  
  K. Guo et al., *“Generating Diverse and Natural 3D Human Motions from Text,”* CVPR 2022.

- **HybrIK: Monocular 3D human pose estimation**  
  J. Li et al., *“HybrIK: A Hybrid Analytical-Neural Inverse Kinematics Solution for 3D Human Pose and Shape Estimation,”* CVPR 2021.

- **SMPL body model**  
  M. Loper et al., *“SMPL: A Skinned Multi-Person Linear Model,”* SIGGRAPH Asia 2015.

See the paper’s `references.bib` for full BibTeX entries.
