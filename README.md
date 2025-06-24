# GroundHog  

## 🌐 Recommended: Run in [Kaggle Notebooks](https://www.kaggle.com) for smooth data handling and reproducibility.

---

## 📁 Directory Index  

- **GroundHog: Pseudo Groundtruth Generator final**  
  Notebook for generating high-resolution annual maximum and minimum groundwater levels (GWL) from sparse in-situ data. This creates a 2 km resolution "pseudo-ground truth" dataset.

- **GroundHog for training upsampling model**  
  Trains a model to upsample coarse-resolution (25 km) GLDAS Groundwater Storage (GWS) data into high-resolution (2 km) GWL estimates. This is the core AI model.

- **GroundHog: Downstream task**  
  Performs downstream engineering tasks, including annual recharge calculation and trend analysis using the GWL predictions from the upsampling model.

> ✅ **All required data is embedded within the notebooks. No external downloads required.**

---

## 🚧 Research Gaps Addressed  

Despite the growing body of literature on groundwater modeling using remote sensing and machine learning, significant gaps remain:

### 1. **Training-Inference Resolution Mismatch**  
Most existing methods train ML models using **coarse-resolution** satellite data (e.g., 25 km GLDAS GWS) and corresponding **low-resolution** in-situ GWL data. However, during inference, they apply these models to **high-resolution** auxiliary data (such as land cover, elevation, or vegetation indices) in an attempt to generate fine-scale predictions.

> 🔍 This approach is fundamentally inconsistent: the model never learns how high-resolution features relate to fine-scale GWL during training. As a result, its high-resolution predictions during inference are poorly grounded and often unreliable.

### 2. **Interpolation-Based Ground Truth Construction**  
Many models rely on **spatial interpolation** (e.g., kriging or IDW) of sparse in-situ GWL data to create dense "ground truth" surfaces. These interpolated values are then used to supervise ML models.

> ⚠️ Interpolation assumes smooth variation and ignores the non-linear influence of hydrogeological features, leading to oversimplified and inaccurate ground truth labels.

### 3. **Year-Dependent Models with Poor Generalization**  
Previous studies often train **separate models for each year**, making them ineffective for long-term forecasting or analysis beyond the years used during training.

> 🔄 This year-wise dependency limits real-world deployment where predictions are needed for unseen years or future scenarios.

---

## 🌍 Significance of the Research  

GroundHog addresses these limitations by introducing a **year-independent**, **AI-powered tabular upsampling method** that learns to translate low-resolution satellite GWS into high-resolution GWL based on both coarse and fine-scale features.

- Enables generation of GWL maps for **any location** and **any year** within the GLDAS archive (2003 onward).
- Supports **fine-scale recharge estimation** and trend analysis without expensive piezometer networks.
- Introduces a scalable framework for combining sparse in-situ data with public satellite data to inform **policy and water resource management**.

---

## 🔧 Method Overview  

### 🧩 Task 1: Generate High-Resolution Pseudo-Ground Truth  
- Notebook: `GroundHog: Pseudo Groundtruth Generator final.ipynb`
- Trains two Random Forest models:
  - Max GWL Model: Predicts maximum annual GWL using 17 hydrogeological factors (HGFs) + year.
  - Min GWL Model: Predicts minimum annual GWL conditioned on max GWL output.
- Combines in-situ data and model predictions to create a **2 km resolution pseudo-ground truth** for 2001–2022.

### 🔄 Task 2: Train the Upsampling Model (GroundHog Core)  
- Notebook: `GroundHog for training upsampling model.ipynb`
- Merges low-resolution GLDAS GWS and grid-level HGFs with high-resolution HGFs for each 2 km point.
- Trains a Random Forest model to map from 25 km GWS + HGFs to 2 km max/min GWL.
- **Key Innovation**: Upsampling is trained and evaluated on actual high-resolution pseudo-ground truth data, not interpolated surfaces.

### 📉 Task 3: Recharge Estimation and Trend Analysis  
- Notebook: `GroundHog: Downstream task.ipynb`
- Computes recharge using the difference between downscaled min and max GWL.
- Applies Mann-Kendall trend test and Sen’s slope estimator to analyze spatio-temporal trends (2003–2022).
- Produces actionable insights for long-term groundwater sustainability planning.

---

## 📊 Output & Results  

- **Pseudo-ground truth R² scores**:  
  - Max GWL Model: 0.855  
  - Min GWL Model: 0.963  

- **Upsampling model R² score**:  
  - LOYO (Leave-One-Year-Out) Average: 0.9275  
  - Inference on unseen year (2024): High visual and statistical fidelity

- **Trend insights**:  
  - Revealed declining recharge in key regions.
  - Identified critical groundwater depletion hotspots.

---

## 🛠️ Technologies Used  

- Python (Pandas, NumPy, Scikit-learn, XGBoost)  
- Geospatial: ArcGIS, Rasterio  
- ML Models: Random Forest  
- Visualization: Matplotlib, Seaborn  
- Platform: Kaggle Notebooks

---

## 🌐 Web Application  

We also provide a simple, interactive web tool to visualize outputs:
- Access: [GroundHog Web Tool (Streamlit)](https://groundhog_streamlit_link.com)  
- Features:
  - Query high-resolution GWL predictions by year and location
  - View recharge maps and temporal trends

---

## 📥 Citation  

If you use GroundHog in your research or project, please cite:

