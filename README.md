# Spotify Top 2000 Analysis

This repository contains a Jupyter notebook, raw data, and all supporting files for exploring and answering a set of analytical questions about the “Top 2000” Spotify playlist as captured in 2019/2020. The goal is to evaluate which questions can be answered directly from this dataset, identify where external data is needed, and outline the transformations required to make the data analytics-ready.

---

## 📁 Repository Structure

```
.
├── README.md                  ← this file
├── spotify_2000.txt           ← raw tab-delimited export of Top 2000 playlist
├── requirements.txt           ← Python package dependencies
└── main.ipynb                 ← Jupyter notebook with ETL steps, exploration & visuals
```

---

## ⚙️ Installation & Setup

1. **Clone this repository**

   ```bash
   git clone https://github.com/your-org/spotify-top2000.git
   cd spotify-top2000
   ```
2. **Create & activate a virtual environment** (recommended)

   ```bash
   python3 -m venv venv
   source venv/bin/activate
   ```
3. **Install dependencies**

   ```bash
   pip install -r requirements.txt
   ```
4. **Launch the notebook**

   ```bash
   jupyter lab main.ipynb
   ```

---

## 📊 Data Overview

* **Source:** Spotify Top 2000 playlist as of 2019/20 (exported to `spotify_2000.txt`).

* **Format:** Tab-delimited text with headers:

  ```
  Index, Title, Artist, Top Genre, Year,
  Beats Per Minute (BPM), Energy, Danceability,
  Loudness (dB), Liveness, Valence,
  Length (Duration), Acousticness, Speechiness, Popularity
  ```

* **Dependencies:** See `requirements.txt` for all required Python packages (pandas, matplotlib, etc.).

---

## 🎯 Analytical Questions & Feasibility

| Question                                                                 | Feasible with this data? | Notes                                                                                           |
| ------------------------------------------------------------------------ | ------------------------ | ----------------------------------------------------------------------------------------------- |
| 1. How many hip hop songs are in the Top 2000 playlist?                  | **Partially**            | We have “Top Genre” (e.g. “detroit hip hop,” “alternative hip hop”), but no sub-genre listing.  |
| 2. Have playlist songs got faster or slower over the years of release?   | **Partially**            | BPM and Year are present—can track tempo trends, but “speed” is genre-dependent.                |
| 3. Which artist or group has the most songs in the playlist?             | **Yes**                  | Straight count by Artist yields Queen, The Beatles, Coldplay as top three.                      |
| 4. How many songs are in English?                                        | **No**                   | No language metadata. Requires external language table or Spotify API enrichment.               |
| 5. If a song is popular on Spotify, is it more likely loud or danceable? | **No** (with caution)    | Popularity vs. loudness/danceability can be correlated, but sample is limited to Top 2000 only. |

---

## 🔄 Required Transformations & ETL Steps

Before handing this data off to analysts or visualization tools (e.g. Power BI, Tableau, etc.), the following transformations are recommended:

1. **Convert to CSV**

   * Parse `spotify_2000.txt` into a well-formed CSV (comma-separated), ensuring proper escaping of titles/artists containing commas or quotes.

2. **Standardize Column Names**

   * Rename ambiguous columns for clarity (e.g. `Valence` → `Positiveness`, `Energy` → `Intensity`).
   * Remove parentheses from headers:

     ```
     Beats Per Minute (BPM) → BPM
     Loudness (dB) → Loudness_dB
     Length (Duration) → Duration_s
     ```

3. **Normalize Data Types**

   * Convert Year to integer.
   * BPM, Energy, Danceability, etc. to numeric types.
   * Duration in seconds (or mm\:ss) parsed to integer seconds.
   * Ensure Popularity is integer percentage (0–100 scale).

4. **Handle Duplicates & Gaps**

   * Identify and remove exact duplicate rows (all columns identical).
   * Verify no missing values in critical columns (Year, BPM, Artist, Title).
   * Log and report any unexpected gaps.

5. **Add Derived Columns**

   * **Language**: enrich via Spotify API lookup (e.g. track object’s `language` field).
   * **Decade**: group Year into decade buckets (e.g. 2000s, 2010s).
   * **Loudness Category**: bucket Loudness\_dB into “Soft”/“Moderate”/“Loud”.

6. **Schema Validation**

   * Define and enforce a schema (e.g. via `pandera` or SQL DDL) for continuous CI/CD checks.

