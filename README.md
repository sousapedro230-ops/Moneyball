⚽ Moneyball for Midfielders: Identifying Undervalued Talent with K-Means Clustering

A data-driven project applying the Moneyball philosophy to football — using statistics and unsupervised machine learning to identify undervalued young midfielders across Europe's top 5 leagues, based on performance relative to market value.

📌 Project Overview

The "Moneyball" approach is about finding value the market is mispricing — using data instead of reputation to spot talent before everyone else does. This project applies that logic to football's hardest position to standardize statistically: the midfield.

A player labeled "MF" in a dataset can be a destroyer, a creator, or a goal-threat — three very different jobs collapsed into one tag. Rather than trust that single label, this project lets K-Means clustering split midfielders into natural playing-style groups, then ranks players within each group by an undervaluation score that compares on-pitch output to market price.

🎯 Objective

Find the most undervalued midfielders under 26 in the top 5 European leagues by:

Filtering for a comparable, meaningful pool of players Building a composite performance score from per-90 statistics Clustering players into defensive, creative, and offensive midfield archetypes (K-Means) Ranking the most undervalued players within each archetype (performance vs. market value)

🔍 Methodology

Data Sources:

Performance data: FBref-style statistics for the 2025/2026 season found in Kaggle (players_data-2025_2026.csv) Market value data: a separate dataset of player market valuations based on Transfermarkt found in Kaggle (players_with_values.csv), merged in by player name

Cleaning & Preparation:

Removed duplicate player entries Filtered to players whose primary position is MF (Midfielder) Split the competition column into country code and league name; mapped country/nationality codes to full names (via pycountry, with manual exceptions for codes it doesn't cover, e.g. ENG, SCO, KVX) Dropped columns with excessive missing values Converted raw counting stats into per-90-minute rates (Gls/90, Ast/90, Fls/90, TklW/90) so players with different playing time are comparable

Filtering Criteria:

FilterThresholdRationalePositionMF onlyFocus the analysis on a single position group, Age ≤ 26, Target players with development/resale upside (Total minutes≥ 300). Exclude small, unreliable samples (Minutes per 90 ≤ 30).

Exclude already-established starters (Market value ≤ €50M) Exclude already-recognized elite players (e.g. Bellingham-tier valuations), which would distort the "undervalued" framing Market value present.

RequiredPlayers without a market value couldn't be scored and were dropped

Performance Score:

Five per-90 features (Gls/90, Ast/90, Sh/90, Fls/90, TklW/90) were standardized with StandardScaler and averaged into a single Performance Score, after first checking a correlation matrix to validate the features weren't redundant.

This is a deliberate simplification: all five features are weighted equally rather than weighted differently per role (e.g. valuing TklW/90 more for a defensive midfielder), because role-specific weighting would require knowing the cluster before computing the score — creating circularity. Equal weighting keeps the score neutral and reproducible.

Known limitation: 

Fls/90 (fouls committed) is ambiguous — it can reflect useful defensive pressure or simple indiscipline. It's included with equal weight, which is a recognized simplification of the model.

Undervaluation Score
Undervaluation Score = Market Value / Performance Score

A player with a high Performance Score and low Market Value produces a high score — flagging a market inefficiency. Players with Performance Score ≤ 0 (below-average across all dimensions) were excluded, since the ratio loses economic meaning at or below that baseline. This filtering step removed roughly 61% of the initial pool — a significant limitation discussed below.

Clustering (K-Means):

The five standardized per-90 features were clustered using K-Means (k=3), with the choice of k validated two ways:

Elbow method — inertia shows a clear inflection at k=3 Silhouette score — computed across k=2 to k=9 to confirm cluster cohesion/separation at k=3

The resulting clusters mapped cleanly onto football intuition:

Cluster 0 — Defensive Midfielders: lower shot volume, higher fouls committed and tackles won Cluster 1 — Creative Midfielders: balanced, moderate output across all features Cluster 2 — Offensive Midfielders: higher goals and shots, fewer defensive actions

A PCA projection (2 components) was used to visualize the cluster separation in 2D and sanity-check the grouping.

📊 Visualizations & Outputs

Age distribution, histogram of the filtered sample, Correlation heatmap of the five performance features, Elbow + Silhouette comparison chart for choosing k, PCA scatter plot of clusters, labeled by player, Bar chart comparing average market value by cluster, Interactive Plotly bar chart: top 10 undervalued players per cluster (switchable by button), with market value shown on hover, Radar charts comparing the top 10 undervalued players within each cluster across all five normalized metrics, An interactive widget (via ipywidgets) letting users filter the top-10 undervalued list by nationality, league, and cluster

💡 Key Findings

Arthur (Cluster 0 — defensive) stood out with a notably higher undervaluation score than any other player, suggesting a low-cost signing who outperformed expectations relative to his market value. Within the creative cluster, Calebe emerged as the standout with the highest potential. Within the offensive cluster, Alisson Santos ranked as the most promising undervalued player. The clustering confirmed that grouping by statistical profile, rather than a single "MF" label, surfaces meaningfully different player types.

⚠️ Limitations

61% of the initial filtered pool was excluded for having a non-positive Performance Score, substantially shrinking the final candidate list — likely a mix of genuinely low output and players whose contributions aren't captured by the five chosen features. The feature set (Gls/90, Ast/90, Sh/90, Fls/90, TklW/90) doesn't include passing or dribbling metrics (e.g. progressive passes, key passes, successful dribbles), which would likely sharpen the separation between creative and offensive midfielders. Goalkeeper-adjacent and possession-based metrics were unavailable in the source dataset, limiting how complete the underlying performance picture could be.

🛠️ Tech Stack

Python — pandas, numpy scikit-learn — StandardScaler, KMeans, PCA, silhouette_score matplotlib / seaborn — static visualizations (histograms, heatmaps, elbow/silhouette plots) Plotly (graph_objects, express) — interactive bar charts and radar charts ipywidgets — interactive filtering dashboard within the notebook pycountry — nationality/country code mapping

📁 Repository Structure

├── Moneyball-main/ │ ├── players_data-2025_2026.csv │ └── players_with_values.csv ├── Moneyball_Melhorado.ipynb └── README.md

🚀 How to Run

Create Codespaces.

Inside do $pip install -r Requirements.txt 

Then add the python extention and you're good to go

🔮 Next Steps

Add passing/dribbling metrics (progressive passes, key passes, successful dribbles) to better separate creative from offensive profiles 

Investigate the 61% drop-out rate to understand whether it reflects genuine underperformance or feature gaps Test role-specific weighting schemes for the Performance Score (e.g. via a two-stage model that clusters first, then re-scores within cluster) 

Package the interactive widget as a standalone Streamlit or Power BI dashboard for non-technical use

👤 About

Built by Pedro Sousa as part of a transition into Data Analytics / Data Science, combining a background in B2B sales with an analytical toolkit (Python, SQL, statistics) developed at ISCTE – Instituto Universitário de Lisboa.

📫 LinkedIn: [https://www.linkedin.com/in/pedrofpsousa] | E-mail: [sousa.pedro230@gmail.com]

Performance data sourced from FBref-style statistics; market values from a separate valuations dataset. Used for educational and portfolio purposes.
