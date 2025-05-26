

## Project Overview & Objectives

# Mapping Water Bodies and Vegetation with Remote Sensing and Machine Learning

This project uses Sentinel-2 satellite imagery to classify land and water areas using remote sensing techniques and machine learning. We explore the difference between Level-1C (Top-of-Atmosphere) and Level-2A (Surface Reflectance) data, compute NDVI and NDWI indices, and apply clustering (K-Means) and supervised learning (Random Forest) to map features.

---
## Project Overview & Objectives
### 1. Problem Statement

Accurate mapping of surface water and vegetation is essential for land-use monitoring, environmental management, and climate research. This project compares atmospheric correction effects between L1C and L2A products using NDVI. It further explores water detection using NDWI, followed by clustering and classification using K-Means and Random Forest, trained on a NDWI-derived baseline.
#### 1.1 Problem Description

Accurate mapping of surface features such as vegetation and water bodies is essential for environmental monitoring, land-use planning, disaster response, and climate change studies. With the increasing availability of satellite data, especially from platforms like Sentinel-2, remote sensing has become a powerful tool for large-scale analysis.

This project addresses the challenge of distinguishing between water and land features in Sentinel-2 imagery using a combination of traditional remote sensing indices and machine learning techniques. Specifically, we:

- Assess the effect of atmospheric correction by comparing NDVI computed from Level-1C (top-of-atmosphere reflectance) and Level-2A (surface reflectance) Sentinel-2 products.
- Use NDWI (Normalized Difference Water Index) to identify water bodies and generate binary masks for supervised learning.
- Apply unsupervised K-Means clustering on both NDVI alone and a combined NDVI+NDWI feature space to segment the image into land cover classes.
- Train a supervised Random Forest classifier using raw spectral bands and derived indices, with the NDWI mask as a pseudo ground-truth.
- Visualize the separability of land and water features using PCA and UMAP dimensionality reduction techniques.

The ultimate goal is to evaluate how well machine learning can replicate or improve upon traditional index-based water detection in remote sensing workflows.

---

### 2. Data Location

All Sentinel-2 and auxiliary data (e.g., MODIS or CORINE) are stored in the following Google Drive folder:

**[[Google Drive Link]](https://drive.google.com/drive/folders/1qrzCOUFN0HxSSXjxvkTllgiAwHWXsbrK?usp=sharing)**

Folders include:
- `S2A_MSIL1C_...SAFE/`: Sentinel-2 L1C imagery (top-of-atmosphere)
- `S2C_MSIL2A_...SAFE/`: Sentinel-2 L2A imagery (surface reflectance)
- Any other optional auxiliary datasets (e.g., MODIS, CORINE)

Note: Within each `.SAFE` folder, `GRANULE` contains `.jp2` band images, which are loaded directly into Colab.

---



## 2. Methodology Diagrams (Remote Sensing and AI Implementation)

To fulfill the requirement of illustrating both the remote sensing techniques and the AI pipeline used in this project, the following diagrams provide a clear visual summary.

---

### **Figure 1: NDWI vs. K-Means Classification Flow**

<img width="499" alt="截屏2025-05-26 19 48 37" src="https://github.com/user-attachments/assets/52777800-2f24-4134-a59a-8010d755c0fe" />

This flowchart represents the core comparison of two approaches to surface water detection:

- **Left branch (Remote Sensing)**: NDWI (Normalized Difference Water Index) is thresholded at 0 to generate a binary water vs. land mask, based on established hydrological interpretation.
  
- **Right branch (AI Clustering)**: NDVI and NDWI images are used as input to a K-Means clustering model (unsupervised machine learning). The resulting clusters are manually mapped to water or land classes based on color inspection or NDWI magnitude.

Both approaches lead to the same evaluation step: a **confusion matrix, accuracy score, and classification report** comparing the AI output with the NDWI-derived “truth.” The result is visualized as side-by-side classification maps.

---

### **Figure 2: Full Remote Sensing + AI Pipeline**
<img width="907" alt="截屏2025-05-26 20 44 40" src="https://github.com/user-attachments/assets/ca31facd-911a-404c-86b8-5918db0708c7" />

This more detailed diagram presents the complete flow of the project from start to finish:

- **Data Input**: Sentinel-2 Level-2A and Level-1C bands (B02, B04, B08).
  
- **Index Calculation**:
  - NDVI from B08 and B04 (vegetation indicator).
  - NDWI from B02 and B08 (water indicator).
  - NDVI differences between L1C and L2A are also assessed to analyze atmospheric correction effects.

- **Masking and Clustering**:
  - Binary water masks are created by thresholding NDWI.
  - K-Means clustering is performed on both NDVI and NDWI, with clusters manually labeled.

- **Supervised Learning**:
  - Random Forest is trained using features: [B02, B04, B08, NDVI, NDWI].
  - NDWI mask is used as a pseudo-ground-truth for labels.

- **Evaluation and Output**:
  - Confusion matrices and accuracy scores are computed.
  - Visual maps are produced to compare predicted vs baseline classifications.

---

## 3. GitHub Repository and Code Documentation

This GitHub repository contains all code, figures, and documentation required to reproduce the project results.

It is structured for clarity and reproducibility, following best practices in remote sensing workflows and machine learning pipelines.

---

### 3.1 Repository Structure

```
/project-root
├── notebooks/
│   └── final_pipeline.ipynb         # Main Google Colab-compatible notebook
│
├── figures/
│   ├── fig1_ndwi_kmeans.jpeg        # Simplified NDWI vs. K-Means workflow
│   ├── fig2_full_pipeline.jpeg      # Full AI + remote sensing pipeline
│
├── requirements.txt                 # Python dependencies (optional)
└── README.md                        # Full project description and usage
```

### 3.2 How to Use This Repository

**Step 1: Open notebook in Google Colab**  

**Step 2: Mount Google Drive and install dependencies**

## 4. Results and Analysis

### 4.1 NDVI (L1C vs. L2A)

We compared NDVI values derived from Level-1C (top-of-atmosphere) and Level-2A (surface reflectance) Sentinel-2 products over the same region. Visually, the L2A NDVI map showed better contrast and vegetation detail.

- **Mean NDVI difference (L2A - L1C)**: **+0.0238**
- **Standard deviation**: **0.1161**

The histogram confirms that most pixels had slightly higher NDVI after atmospheric correction. This validates the use of Level-2A data for more accurate vegetation mapping.

---

### 4.2 NDWI Thresholding vs. K-Means Clustering

#### NDWI Thresholding
Using a simple threshold (NDWI > 0), we created a binary mask classifying water and land. This serves as our pseudo-ground truth for comparison.

#### K-Means Clustering
We performed unsupervised clustering (k=4) on:
- NDVI only
- NDVI + NDWI

Clusters were manually grouped into water vs. land based on visual inspection. When compared to the NDWI mask:

- **Accuracy**: **88.4%**
- **Precision (water)**: **0.86**
- **Recall (water)**: **1.00**
- **F1-score (water)**: **0.93**

Confusion matrix showed excellent detection of water pixels, though some land areas were misclassified. Visual comparison revealed that K-Means successfully identified major water bodies.

---

### 4.3 Random Forest Classification

A Random Forest (RF) classifier was trained using:
- Input features: B02, B04, B08, NDVI, and NDWI
- Labels: NDWI mask (water vs. land)

On the test set:

- **Accuracy**: **100.0%**
- **Precision & Recall**: **1.00** for both water and land
- **Confusion Matrix**: Perfect classification

This shows the RF model was able to perfectly replicate the NDWI-derived water mask, indicating high generalization using only spectral features and vegetation/water indices.

---
### 4.4 Dimensionality Reduction (PCA & UMAP)

To better understand the internal structure of our 5-band feature space, we applied dimensionality reduction techniques:

#### PCA (Principal Component Analysis)

We performed PCA on the standardized stack of five features: B02, B04, B08, NDVI, and NDWI. The resulting 2D projection shows distinct separation between K-Means clusters.

- Water pixels (Cluster 0) formed a dense, compact region in feature space
- Vegetation and land clusters were more dispersed and variable
- This supports the idea that water features are more spectrally uniform than vegetated areas

### 4.5 Visual Comparison of Methods
```

| Image                            | Description                             |
|----------------------------------|-----------------------------------------|
| NDVI L1C & L2A                   | Shows how reflectance correction improves detail |
| NDVI Difference                  | L2A generally higher than L1C NDVI      |
| K-Means (NDVI only)              | Classifies broad land types, misses finer water edges |
| K-Means (NDVI + NDWI)            | More accurate water separation          |
| NDWI Binary Mask                 | Clean mask used as baseline ground truth |
| NDWI vs. K-Means (Binary)        | Side-by-side output for confusion matrix |
| Random Forest vs. NDWI Baseline | Perfect match, confirming model robustness |

```

### 4.6 Summary Table
```

| Method                  | Accuracy | Precision | Recall | External Labels? | Interpretability |
|-------------------------|----------|-----------|--------|------------------|------------------|
| NDWI Thresholding       | N/A      | N/A       | N/A    | No               | Very High        |
| K-Means (NDVI + NDWI)   | 88.4%    | 0.86      | 1.00   | No               | Moderate         |
| Random Forest (RF)      | 100%     | 1.00      | 1.00   | NDWI Mask        | High             |

```
