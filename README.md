# Market-Regime-Detection-with-PCA-and-Clustering
Notebook on macro–market regime detection. Builds daily and monthly FRED dataset (SPX, NASDAQ, yields, VIX, CPI, unemployment, INDPRO), engineers financial and macro features, applies PCA and KMeans to extract 3 regimes, and interprets dynamics via profiles, transition matrix, durations, and timelines.

## Dataset

**Source:**  [FRED — Federal Reserve Economic Data](https://fred.stlouisfed.org)  
Data is fetched programmatically via the CSV export endpoint:  
`https://fred.stlouisfed.org/graph/fredgraph.csv?id=<SERIES_ID>`  

**Series used:**
- **Daily market series**:  
  - SPX (S&P 500), NASDAQ  
  - Treasury yields (2y, 10y, 30y, 3m T-bill)  
  - VIX (volatility index)

- **Monthly macro series** (aligned to release dates to avoid lookahead bias):  
  - CPI (inflation)  
  - UNRATE (unemployment)  
  - INDPRO (industrial production)

## Project Workflow

### 1. Data Loading and Preprocessing 
- Loaded daily and monthly macro/market series.  
- Aligned monthly releases with realistic publication.   
- Constructed a unified daily DataFrame with equity, rates, volatility, and macro indicators.

### 2. Feature Engineering
- **Returns and momentum**: log returns at 1, 5, 21, 63-day horizons.  
- **Bond proxy**: price move via daily changes in 10y yield.  
- **Volatility**: 21-day rolling realized vol for SPX and NASDAQ.  
- **Correlations**: 60-day rolling equity–equity (SPX vs NASDAQ) and equity–bond correlations.  
- **Yield curve factors**: level (10y), slope (10y–3m), curvature (2y+30y–2·10y).  
- **Macro anchors**: YoY CPI, unemployment level & 21-day diff, industrial production YoY.  
- **Winsorization** of heavy-tailed features at 1% / 99%.  
- **Standardization** with `StandardScaler` (suffix `_z`).

### 3. Dimensionality Reduction (PCA)
- Applied PCA to standardized features.  
- Scree plot and explained variance show ~70% captured by 5 PCs.  
- Interpretation of first 3 PCs:  
  - PC1: equity risk / volatility factor.  
  - PC2: macro vs stress tradeoff.  
  - PC3: yield-curve structure vs growth signals.  
- PCA loadings plotted for interpretability.

### 4. Clustering
- Reduced dataset to 2 PCs for visualization.  
- Applied KMeans (k=3) to extract market regimes.  
- Computed cluster profiles (mean values of key features per regime).  
- Calculated Markov transition matrix, regime durations, and timeline.

### 5. Evaluation and Visualization
- Plotted:
  - Scree plot and PCA loadings.  
  - Cluster scatter in PCA space.  
  - Regime timeline (daily labels).  
  - Heatmap of regime profiles.  
 
## Results
The analysis uncovers three persistent market regimes:

- **Crisis regime (~7%)** – rare but severe, marked by equity collapses, volatility spikes, high unemployment, and strong flight-to-safety into Treasuries (deeply negative stock–bond correlation).  
  These episodes align with the 2018 Fed tightening shock, the 2020 COVID crash, and volatility surges in 2022–2023.

- **Risk-off corrections (~20%)** – more frequent stress periods with rising volatility and moderate selloffs, but without systemic breakdown.  
  Bonds continue to hedge equities, and unemployment/inflation remain moderate, capturing flight-to-quality episodes typical of market pullbacks.

- **Risk-on, Stable growth (~73%)** – the dominant long-run state, featuring rising equities, subdued volatility, supportive macro fundamentals, and negative stock–bond correlations.  
  This regime shows high persistence (~95%), reflecting the “normal” functioning of markets.


Overall, the clustering provides an interpretable macro–market state model that distinguishes between crisis, correction, and stable growth environments, linking quantitative profiles to real historical events.
