# Movie Recommender System (Content-Based & Hybrid)

This repository contains a complete pipeline to build a robust **Movie Recommendation System** using data from official IMDb datasets. It features both a **Content-Based Recommender** using metadata (genres, cast, directors, writers) and a **Hybrid Recommender** that balances content similarity with popularity-based reliability using IMDb's weighted rating formula.

## Features
- **Efficient Data Pipeline**: Reads and filters massive IMDb datasets in real-time chunks to keep memory usage low.
- **Advanced Feature Engineering**: Creates a metadata "soup" combining the top 3 cast members, directors, writers, and movie genres.
- **IMDb Weighted Rating Integration**: Utilizes IMDb's formula to compute fair quality scores, preventing niche movies with very few votes from skewing the results.
- **Hybrid Recommendation Engine**: Blends metadata cosine similarity scores and IMDb weighted rating scores with an adjustable `alpha` parameter.

---

## Datasets Used
This project dynamically pulls gzipped TSV files directly from [IMDb Datasets](https://datasets.imdbws.com/):
1. `title.basics.tsv.gz` - Movie titles, genres, and release years.
2. `title.ratings.tsv.gz` - User ratings and vote counts.
3. `title.crew.tsv.gz` - Director and writer mappings.
4. `name.basics.tsv.gz` - Cast and crew actual names.

---

## Installation & Setup

### Prerequisites
Make sure you have Python 3.8+ installed along with the required libraries:

```bash
pip install pandas numpy scikit-learn
```

### Running the Project
1. Clone this repository:
   ```bash
   git clone https://github.com/your-username/movie-recommender.git
   cd movie-recommender
   ```
2. Open the notebook in Google Colab or your local Jupyter environment and run all cells.

---

## How It Works

### 1. Data Processing & Imputation
- Modern movies (released between 2000 and 2026) are targeted.
- Missing runtimes are handled elegantly using scikit-learn's `KNNImputer`.
- Gzipped IDs (nconsts/tconsts) are resolved to their actual actor and crew names.

### 2. IMDb Weighted Rating Formula
To rank recommendations fairly, we use:
$$\text{Weighted Rating } (W) = \left(\frac{v}{v+m}\right) \cdot R + \left(\frac{m}{v+m}\right) \cdot C$$
Where:
- $v$: Number of votes for the movie
- $m$: Minimum votes required to be listed (80th percentile)
- $R$: Average rating of the movie
- $C$: Mean rating across the whole dataset

### 3. Natural Language Processing
A "soup" string of metadata is vectorized using `CountVectorizer` to generate a token occurrence matrix. Then, **Cosine Similarity** is calculated to find movies with similar thematic compositions and crew.

### 4. Hybrid Scoring
The recommendation score is computed as:
$$\text{Hybrid Score} = \alpha \cdot \text{Similarity} + (1 - \alpha) \cdot \text{Normalized Weighted Rating}$$
- $\alpha = 1.0$: Purely Content-Based.
- $\alpha = 0.0$: Purely Rating/Popularity-Based.
- $\alpha = 0.5$ (Default): Perfect balance of both.

---

## Demo & Usage

Once the script runs, it will prompt you to input a movie title:
```python
# Get recommendations
rec_df = hybrid_recommender("Kate & Leopold", top_n=10, alpha=0.5)
display(rec_df[['primaryTitle', 'genres', 'averageRating', 'score']])
```
