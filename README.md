# SmartCart — E-Commerce Customer Segmentation System

SmartCart is a customer segmentation system for e-commerce data. It cleans and engineers features from raw customer records, then compares **K-Means** and **Agglomerative (Hierarchical) Clustering** to group customers into meaningful segments based on income, spending, and purchase behavior — helping identify high-value, budget-conscious, and at-risk customer groups for targeted marketing.

## Overview

SmartCart takes raw customer transaction and demographic data and turns it into actionable customer segments. The pipeline:

1. **Cleans** the data (handles missing income values).
2. **Engineers features** — Age, Customer Tenure, Total Spending, Total Children, simplified Education levels, and simplified Marital Status.
3. **Removes outliers** in age and income.
4. **Encodes and scales** categorical and numerical features.
5. **Reduces dimensionality** with PCA for visualization and clustering.
6. **Determines the optimal number of clusters** using the Elbow Method and Silhouette Score.
7. **Clusters customers** with both K-Means and Agglomerative Clustering, and compares them.
8. **Profiles each segment** (average income, spending, recency, purchase channels, etc.) to make the results business-usable.

## Why Two Clustering Algorithms?

SmartCart runs **K-Means** and **Agglomerative Clustering** side by side on the same PCA-reduced feature space so the results can be visually and statistically compared rather than assuming one algorithm is correct upfront. This lets the notebook pick whichever produces tighter, more separable, and more interpretable customer segments instead of committing to a single method by default.

## Tech Stack

- **Python**
- **pandas / NumPy** — data manipulation
- **matplotlib / seaborn** — visualization (pairplots, heatmaps, 3D scatter plots)
- **scikit-learn** — `OneHotEncoder`, `StandardScaler`, `PCA`, `KMeans`, `AgglomerativeClustering`, `silhouette_score`
- **kneed** — automatic elbow-point detection

## Dataset

The notebook expects a `smartcart_customers.csv` file with customer demographic and purchase-history fields, including:

- `Year_Birth`, `Education`, `Marital_Status`, `Income`
- `Kidhome`, `Teenhome`, `Dt_Customer`, `Recency`
- Spending columns: `MntWines`, `MntFruits`, `MntMeatProducts`, `MntFishProducts`, `MntSweetProducts`, `MntGoldProds`
- Purchase channel columns: `NumDealsPurchases`, `NumWebPurchases`, `NumCatalogPurchases`, `NumStorePurchases`, `NumWebVisitsMonth`
- `Response`

> Place `smartcart_customers.csv` in the project root before running the notebook.

## Project Structure

```
SmartCart/
├── smartcart.ipynb          # Main notebook: EDA, preprocessing, clustering, evaluation
├── smartcart_customers.csv  # Input dataset (not included — add your own)
└── README.md
```

## How It Works

### 1. Data Preprocessing
Missing `Income` values are filled with the median. Categorical features are simplified:
- **Education** → `Undergraduate`, `Graduate`, `Postgraduate`
- **Marital Status** → `Alone` or `Partner`

### 2. Feature Engineering
- `Age` — derived from `Year_Birth`
- `Customer_Tenure_Days` — days since enrollment
- `Total_Spending` — sum of all product spending categories
- `Total_Children` — `Kidhome` + `Teenhome`

### 3. Outlier Removal
Customers older than 90 or with income above 600,000 are excluded to prevent skewed clustering.

### 4. Encoding & Scaling
Categorical features are one-hot encoded; all features are standardized with `StandardScaler`.

### 5. Dimensionality Reduction
`PCA` reduces the feature space to 3 components for clustering and 3D visualization.

### 6. Choosing K
- **Elbow Method** (via `kneed.KneeLocator`) on WCSS
- **Silhouette Score** across a range of K values

Both methods point to **K = 4** as the optimal number of segments.

### 7. Clustering & Comparison
Both `KMeans(n_clusters=4)` and `AgglomerativeClustering(n_clusters=4)` are fit on the PCA-reduced data and visualized in 3D. The resulting cluster labels are compared, and the final segment profiling in the notebook uses the **Agglomerative Clustering** result.

### 8. Cluster Profiling
Each cluster is summarized by mean `Income`, `Recency`, spending, and purchase-channel usage, giving a clear behavioral profile per segment (e.g., high-income/high-spend vs. low-income/deal-driven customers).

## Getting Started

### Prerequisites
```bash
pip install pandas numpy matplotlib seaborn scikit-learn kneed
```

### Run
1. Clone the repo and add your `smartcart_customers.csv` to the project root.
2. Open `smartcart.ipynb` in Jupyter Notebook / JupyterLab / VS Code.
3. Run all cells top to bottom.

## Results

- Optimal cluster count identified: **4**
- PCA retains roughly **45%** of variance across the first 3 components (~23% + 11% + 10%)
- Final segments are compared across income, spending, and purchase-channel behavior to profile distinct customer types (e.g., high-value frequent buyers vs. price-sensitive low spenders)

## Future Improvements

- Add DBSCAN or Gaussian Mixture Models for further comparison
- Automate cluster-to-persona labeling
- Build a simple dashboard (Streamlit/Plotly) for interactive segment exploration
- Add a `requirements.txt` and CLI/script version of the pipeline

## License

No license has been specified for this project — all rights reserved by default.
