# CrossMamba-Agri-Thesis
🎯 Project Overview
Title: CrossMamba-Agri: Cross-Attention Vision Mamba for Joint Pest and Disease Classification
Goal: To develop a unified Vision Mamba (State Space Model) architecture that can classify both plant diseases and insect pests in a single network.
Main Target: Beat Karthik et al. (2024) who achieved 95.68% on the CCMT dataset using a Swin Transformer.

📂 Dataset Processing & Cleaning
1. Raw Dataset (CCMT)
Total Images: 25,220 (22 classes: 13 disease, 4 healthy, 5 pest).

Cleaning: 94 corrupt images and 1,851 near-duplicates (pHash Hamming < 6) removed.

Final Clean Size: 23,275 images.

Split: 80/10/10 per class (Train: 18,613 | Val: 2,316 | Test: 2,346).

2. Augmented Dataset (Karthik's Protocol)
Total Images: 105,252.

Leakage Analysis: Discovered 8.50% leakage (test images sharing parents with train images) in Karthik's split.

Two Splits Created:

Leaked Split (Karthik's): Train 80,271 | Test 24,981 (Used for fair comparison).

Clean Split (Ours): Parent-level split to remove leakage. Train 84,219 | Val 10,565 | Test 10,468.

🏗️ Model Architecture (V2)
The architecture consists of 6 core components, totaling 38.61M parameters:

BSM (Background Suppression Module): Uses FFT channel attention, SimAM, and spatial convolution to filter field noise.

Multi-Scale Patch Embedding: Extracts features at 8x8, 16x16, and 32x32 scales, fusing them into 196 tokens of 512 dimensions.

Bidirectional Mamba Encoder: 8 stacked BiMamba blocks (Forward + Backward SSM scans) for linear O(N) global context.

Adaptive Query Generator: Generates 12 per-image dynamic queries based on the global feature.

Cross-Attention Fusion: Task queries (disease/pest) attend to the shared token sequence.

Dual Task Heads: Separate linear classifiers for Disease (17 classes) and Pest (5 classes).

🏋️ Training & Evaluation Results
Model-A (Trained on Raw CCMT Data)
Protocol: Two-stage (2 warmup + 25 finetune epochs). Batch size 8, Grad accum 2.

Best Test Accuracy: 80.65% (Val Acc: 82.64%).

Disease: 79.00% | Pest: 88.15%.

Model-B (Trained on Leaked Augmented Data)
Protocol: Two-stage (2 warmup + 25 finetune epochs).

Best Test Accuracy (Baseline): 92.03%.

With TTA (Test-Time Augmentation): 93.25%.

Disease: 92.23% | Pest: 98.39%.

Model-B+ (Trained on Clean Augmented Data)
(Pending - Will be trained after fine-tuning).

🚀 Current Phase: Fine-Tuning
We are currently fine-tuning Model-B with a very low Learning Rate (
1
e
−
5
1e−5) for 12 additional epochs.

Reason: To push the baseline Test Accuracy from 92.03% closer to Karthik's 95.68% without causing severe overfitting.

If Fine-tuning + TTA does not cross 95.68%, we will use Ensemble (Weight Averaging) between the baseline and fine-tuned models.
