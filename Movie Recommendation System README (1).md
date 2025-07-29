# 🎬 Movie Recommendation System

A collaborative filtering-based movie recommendation system that suggests movies based on user ratings and correlations. This system uses the MovieLens dataset to find movies similar to a target movie based on user rating patterns.

![Movie Recommendation Demo](https://media1.giphy.com/media/v1.Y2lkPTc5MGI3NjExZnpqNTJyc3hubHlhOTJqeHk5N3V3cW14ZmozNHNvZTZ6ajI4OTEzciZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/SuLUal1Ll4oV4CgyHT/giphy.gif)


## 🌟 Features

- **Collaborative Filtering**: Uses user-item correlation to find similar movies
- **Customizable Thresholds**: Adjustable minimum rating count for reliable recommendations
- **Easy-to-Use Interface**: Simple function call to get movie recommendations
- **Data Visualization**: Clear correlation analysis and rating statistics

## 📋 Table of Contents

- [Installation](#installation)
- [Dataset](#dataset)
- [How It Works](#how-it-works)
- [Usage](#usage)
- [Examples](#examples)
- [API Reference](#api-reference)
- [Contributing](#contributing)
- [License](#license)

## 🚀 Installation

### Prerequisites

Make sure you have Python 3.6+ installed on your system.

### Install Required Packages

```bash
pip install pandas numpy scikit-learn
```

### Dataset Setup

Download the MovieLens 100K dataset:
1. Download from [MovieLens 100K Dataset](https://grouplens.org/datasets/movielens/100k/)
2. Extract the files
3. Ensure `u.data` and `u.item` files are in your project directory

![Dataset Structure](https://upload.wikimedia.org/wikipedia/commons/thumb/c/c3/Python-logo-notext.svg/1200px-Python-logo-notext.svg.png)
*Python data processing - Source: Wikimedia Commons*

## 📊 Dataset

The system uses two main files from the MovieLens 100K dataset:

- **u.data**: User ratings data (user_id, movie_id, rating, timestamp)
- **u.item**: Movie information (movie_id, title, and other metadata)

### Data Format

| File | Columns | Description |
|------|---------|-------------|
| u.data | user_id, movie_id, rating, timestamp | User ratings (1-5 scale) |
| u.item | movie_id, title, release_date, ... | Movie metadata |

## 🔧 How It Works

The recommendation system follows these steps:

![Algorithm Flow](https://www.researchgate.net/profile/Muhammad-Hanif-5/publication/309038723/figure/fig3/AS:416424515420161@1476328806006/General-Machine-Learning-Flow-Chart-Almost-all-of-the-reviewed-articles-make-their.png)
*Machine Learning workflow flowchart - Source: ResearchGate*

### MovieLens Dataset Structure

The system uses the [MovieLens 100K dataset](https://grouplens.org/datasets/movielens/100k/), which contains:
- **100,000 ratings** from 943 users on 1,682 movies
- **Rating scale**: 1-5 stars
- **Sparsity**: ~93.7% (most user-movie pairs are unrated)

### Collaborative Filtering Process

1. **Data Loading**: Merges user ratings with movie titles
2. **Matrix Creation**: Creates a user-movie rating matrix
3. **Correlation Analysis**: Calculates correlation between target movie and all other movies
4. **Filtering**: Applies minimum rating count threshold for reliability
5. **Ranking**: Sorts recommendations by correlation strength

### Mathematical Foundation

The system uses Pearson correlation coefficient to measure similarity:

```
r = Σ(xi - x̄)(yi - ȳ) / √[Σ(xi - x̄)² × Σ(yi - ȳ)²]
```

Where:
- `xi, yi` are individual rating pairs
- `x̄, ȳ` are mean ratings for each movie

## 💻 Usage

### Basic Usage

```python
import pandas as pd

# Load and prepare data
ratings = pd.read_csv('u.data', sep='\t', names=['user_id', 'movie_id', 'rating','timestamp'])
movies = pd.read_csv('u.item', sep='|', encoding='latin-1', names=['movie_id','title'], usecols=[0,1])
data = pd.merge(ratings, movies, on='movie_id')

# Create user-movie matrix
user_movie_matrix = data.pivot_table(index='user_id', columns='title', values='rating')

# Get recommendations
recommendations = recommend("Toy Story (1995)")
print(recommendations)
```

### Advanced Usage

```python
# Custom minimum rating threshold
recommendations = recommend("Star Wars (1977)", min_ratings=100)

# Get top 5 recommendations only
top_5 = recommend("The Matrix (1999)", min_ratings=30).head(5)
```

## 📈 Examples

### Example 1: Star Wars Recommendations

![Star Wars Example](https://cdn.pixabay.com/animation/2022/12/05/11/27/11-27-32-44_512.gif)
*Data analysis animation - Source: Pixabay*

```python
# Find movies similar to Star Wars
star_wars_recs = recommend("Star Wars (1977)", min_ratings=50)
print(star_wars_recs)
```

**Output:**
```
                           Correlation  RatingCount
Empire Strikes Back, The       0.748154          367
Return of the Jedi             0.672556          507
Raiders of the Lost Ark        0.536117          420
Indiana Jones and the Last     0.514988          146
```



```python
# Find movies similar to Toy Story
toy_story_recs = recommend("Toy Story (1995)")
print(toy_story_recs.head())
```

### Example 3: Custom Analysis

```python
# Manual correlation analysis
target_movie = "Titanic (1997)"
target_ratings = user_movie_matrix[target_movie]
similar_movies = user_movie_matrix.corrwith(target_ratings)

# Visualize correlations
import matplotlib.pyplot as plt
similar_movies.hist(bins=50)
plt.title(f'Correlation Distribution for {target_movie}')
plt.show()
```

## 📚 API Reference

### `recommend(movie_name, min_ratings=50)`

Returns movie recommendations based on collaborative filtering.

**Parameters:**
- `movie_name` (str): The exact title of the target movie
- `min_ratings` (int, optional): Minimum number of ratings required (default: 50)

**Returns:**
- `pandas.DataFrame`: Top 10 recommended movies with correlation scores and rating counts

**Example:**
```python
recs = recommend("Forrest Gump (1994)", min_ratings=75)
```

### Data Structures

#### User-Movie Matrix
```python
user_movie_matrix = data.pivot_table(
    index='user_id', 
    columns='title', 
    values='rating'
)
```

#### Correlation DataFrame
```python
corr_df = pd.DataFrame(similar_movies, columns=['Correlation'])
corr_df = corr_df.join(rating_counts)
```

## 📊 Performance Metrics

![Performance Analysis](https://cdn.pixabay.com/animation/2022/12/20/08/46/08-46-34-852_512.gif)
*Analytics and metrics visualization - Source: Pixabay*

| Metric | Value |
|--------|-------|
| Dataset Size | 100,000 ratings |
| Movies | 1,682 unique titles |
| Users | 943 unique users |
| Sparsity | ~93.7% |

## 🔍 Troubleshooting

### Common Issues

1. **Movie Not Found Error**
   ```python
   # Check available movies
   available_movies = user_movie_matrix.columns.tolist()
   print("Available movies:", len(available_movies))
   ```

2. **Low Correlation Scores**
   - Reduce `min_ratings` parameter
   - Check if the movie has sufficient ratings

3. **Data Loading Issues**
   - Verify file paths and encoding
   - Ensure dataset files are properly formatted

## 🚀 Future Enhancements

![Innovation Concept](https://cdn.pixabay.com/animation/2022/07/31/06/27/06-27-32-434_512.gif)
*Technology and innovation - Source: Pixabay*

- [ ] **Matrix Factorization**: Implement SVD-based recommendations
- [ ] **Deep Learning**: Add neural collaborative filtering
- [ ] **Content-Based Filtering**: Include movie genres and metadata
- [ ] **Hybrid Approach**: Combine multiple recommendation techniques
- [ ] **Real-time Updates**: Dynamic model updating
- [ ] **Web Interface**: Flask/Django web application
- [ ] **API Endpoint**: RESTful API for recommendations

## 🤝 Contributing

We welcome contributions! Here's how you can help:

1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b feature/AmazingFeature`)
3. **Commit** your changes (`git commit -m 'Add some AmazingFeature'`)
4. **Push** to the branch (`git push origin feature/AmazingFeature`)
5. **Open** a Pull Request

### Development Setup

```bash
# Clone the repository
git clone https://github.com/yourusername/movie-recommendation-system.git

# Install development dependencies
pip install -r requirements-dev.txt

# Run tests
python -m pytest tests/
```

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- **MovieLens**: For providing the excellent dataset
- **GroupLens Research**: University of Minnesota
- **pandas & scikit-learn**: For the powerful data analysis tools

## 📞 Contact

- **Author**: Your Name
- **Email**: your.email@example.com
- **Project Link**: [https://github.com/yourusername/movie-recommendation-system](https://github.com/yourusername/movie-recommendation-system)

---

⭐ **Star this repository if you found it helpful!**

![GitHub stars](https://img.shields.io/github/stars/yourusername/movie-recommendation-system?style=social)
![GitHub forks](https://img.shields.io/github/forks/yourusername/movie-recommendation-system?style=social)
![GitHub issues](https://img.shields.io/github/issues/yourusername/movie-recommendation-system)
![GitHub license](https://img.shields.io/github/license/yourusername/movie-recommendation-system)