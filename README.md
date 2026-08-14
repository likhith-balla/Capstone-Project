# likhith-balla-Capstone-Project


Open Google Colab or launch local Jupyter Notebook

Import or upload the notebook (.ipynb) file.

Execute all cells sequentially from top to bottom (Shift + Enter).


Books Scraping & Relational Data Pipeline
An end-to-end Python pipeline that scrapes book catalog data from Books to Scrape, cleans and transforms the raw extracted attributes, loads the normalized data into a relational SQLite database, and executes SQL analytics.

Key FeaturesTargeted Web Scraping: Extracts book catalog details for specific categories (Travel, Mystery, Historical Fiction, and Sequential Art).Data Cleaning & Currency Conversion: Cleans raw price strings, handles missing data via median imputation, converts star ratings to numerical values, and computes prices in INR using a conversion rate ($\text{GBP to INR} = 105.50$).Relational Database Integration: Stores normalized categories and books in an SQLite database with primary key/foreign key constraints and validation checks.SQL Analytics & Verification: Runs analytical queries (filtering 5-star items, price ranges, top expensive items, SQL JOINs) and cross-validates SQL output with Pandas merge operations.

Design Decisions
1. Web Scraping Strategy
Target Filtering: Rather than scraping the entire catalog, the script navigates through the category sidebar (div.side_categories) to target four relevant categories (Travel, Mystery, Historical Fiction, Sequential Art).

DOM Parsing: Uses BeautifulSoup to parse HTML structures (article.product_pod), cleanly extracting titles from tag attributes (title), prices (p.price_color), ratings (p.star-rating), and availability.

2. Data Transformation & ResilienceStar Rating Mapping: Maps text-based ratings ("One", "Two", etc.) directly to integer representations ($1$ to $5$) for efficient indexing and querying.Currency Standardization: Multiplies raw GBP values by $105.50$ to generate a dedicated price_inr metric for localized analysis.Null Handling: Includes defensive fallback logic using median imputation (fillna(df[...].median())) in case numeric conversions fail or scraped fields contain missing entries.

3.  Database Schema & NormalizationRelational Design (3NF): Separate categories and books tables to prevent redundancy and establish clean data integrity.Enforced Constraints:Foreign key constraints (PRAGMA foreign_keys = ON;) linking books.category_id to categories.category_id.CHECK constraints on books.rating ($1 \le \text{rating} \le 5$) and books.in_stock ($\text{in\_stock} \in \{0, 1\}$).Dynamic Category Mapping: Categories are inserted with INSERT OR IGNORE to maintain uniqueness, followed by a dictionary lookup to resolve category_id references before populating book records.

4.  Dual-Layer Verification
Validates SQL queries directly against SQLite (pd.read_sql).
Reproduces relational JOIN results in-memory using pd.merge() to verify consistency across both SQL and Pandas execution layers.


Install the required dependencies via pip
Clone or Download the Project
Save the notebook in your workspace directory.
Launch Jupyter
Run the Notebook Cells

Cell 1 (Data Ingestion & Cleaning):
Loads the raw Titanic dataset via Seaborn, saves the raw file to analytics/titanic.csv, executes missing value handling, and outputs analytics/titanic_cleaned.csv.

Cell 2 (Statistical Analysis & Visualization):
Ingests the cleaned dataset, evaluates central tendency metrics and IQR outlier bounds for age and fare, and generates exploratory visual plots.

Key Design Decisions & Methodology
1. Missing Value Strategy
High Missingness Removal (deck): The deck feature exhibits over 77.22% missing values. Imputing this column would introduce high bias and synthetic noise, so it was dropped entirely.

Granular Categorical Dropping (embarked, embark_town): Missing records in embarked and embark_town account for only 0.22% of the dataset (2 rows). Dropping these two rows eliminates nulls without impacting sample size significantly.

Demographic Group Median Imputation (age): Rather than using a single global median/mean for missing age values (~19.87%), missing values are imputed using the median age grouped by passenger class (pclass) and sex (sex). This preserves realistic age distributions across socioeconomic tiers and demographic groups.

 Statistical Metrics & Outlier DetectionIQR Method ($1.5 \times \text{IQR}$):Calculated lower and upper fences to isolate statistical anomalies in numerical variables:Age: 32 outliers identified above the $57.75$ upper bound.Fare: 114 outliers identified above the $\$65.66$ upper bound.Central Tendency & Skewness Analysis:Evaluated Mean, Median, and Mode for passenger fares:Mean: $\$32.10$Median: $\$14.45$Mode: $\$8.05$Because $\text{Mean} > \text{Median} > \text{Mode}$, the analysis confirms a strongly right-skewed (positively skewed) distribution driven by high-value 1st-class ticket fares.

 Visualizations
Dual-Plot Layout: Generated side-by-side Histograms (for distribution density) and Boxplots (for quartile boundaries and extreme outliers) for both age and fare using Seaborn's whitegrid theme.
