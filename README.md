**Netflix Shows & Movies**
```
#Name: Mohana Priya D
#Reg No.: 212225230182
```
**Aim**

To analyze Netflix dataset and compare movies vs TV shows, top producing countries, and release year trends.

**Procedure / Algorithm**

  1)Load dataset (netflix_titles.csv).
  2)Count movies vs TV shows.
  3)Group by country → top contributors.
  4)Create pivot table (release year vs type).
  5)Visualize with bar & line charts.

**Program**
1) Load and Explore the Dataset
```python
import pandas as pd
df = pd.read_csv(
    "https://raw.githubusercontent.com/allenkong221/netflix-titles-dataset/main/netflix_titles.csv"
)

# First 5 rows
df.head()

# Last 5 rows
df.tail()

# Shape
print("Shape:", df.shape)
print("Rows:", df.shape[0])
print("Columns:", df.shape[1])

# Column names
print("Column Names:")
for col in df.columns:
    print(col)

print(df.columns)

# Data types
print(df.dtypes)

# Dataset information
df.info()

# Statistical summary
df.describe()
```
2) Analyze Missing Values
```python
# Identify missing values
df.isnull()
df.isnull().sum()

# Percentage
print((df.isnull().sum() / len(df)) * 100)
print(
    "Director missing percentage:",
    df['director'].isnull().sum() / len(df['director']) * 100
)

# In director
df['director'] = df['director'].fillna('Unknown')

print(
    "Director missing percentage after filling:",
    df['director'].isnull().sum() / len(df['director']) * 100
)

# In country
df['country'] = df['country'].fillna('Unknown')

# In cast
df['cast'] = df['cast'].fillna('Unknown')

# In rating
df['rating'] = df['rating'].fillna('Unknown')

# Missing values after filling
print(df.isnull().sum())

# Remove rows with missing director and country
df_drop = df.dropna(subset=['director', 'country'])

# Display new DataFrame
df_drop.head()
```
3) Perform Data Indexing and Selection
 ```python
#  Movies
movies = df.loc[df['type'] == 'Movie']
print(movies.head())

#  TV Shows
tv_show = df.loc[df['type'] == 'TV Show']
print(tv_show)

# Title, country and release year
print(df[['title', 'country', 'release_year']])

# Rows from index 100 to 120
print(df.loc[100:120])

# Movies released after 2018
print(
    df.loc[
        (df['type'] == 'Movie') &
        (df['release_year'] > 2018)
    ]
)

# Titles produced in India
print(
    df.loc[
        df['country'].str.contains('India', na=False),
        ['title', 'country']
    ]
)

# TV Shows from US
print(
    df.loc[
        (df['type'] == 'TV Show') &
        (df['country'].str.contains('United States', na=False))
    ]
)

# Movies with PG-13 rating
print(
    df.loc[
        (df['type'] == 'Movie') &
        (df['rating'] == 'PG-13')
    ]
)
```
4) Create New Columns
```python
# Title Length
df['Title Length'] = df['title'].str.len()
print(df['Title Length'].head())

# Movie Age
df['Movie Age'] = 2026 - df['release_year']
print(df['Movie Age'])

# Recent Content
df['Is Recent Content'] = df['release_year'] > 2018
print(df['Is Recent Content'])

# Display 
print(
    df[
        [
            'title',
            'release_year',
            'Title Length',
            'Movie Age',
            'Is Recent Content'
        ]
    ].head()
)

# Longest title
longest_index = df['Title Length'].idxmax()
print("Longest title:", df.loc[longest_index, 'title'])

# Shortest title
shortest_index = df['Title Length'].idxmin()
print("Shortest title:", df.loc[shortest_index, 'title'])

# Average Movie Age
print("Average Movie Age:", df['Movie Age'].mean())

# Oldest content
oldest_index = df['release_year'].idxmin()
print(
    "Oldest content:",
    df.loc[oldest_index, ['title', 'release_year']]
)

# Newest content
newest_index = df['release_year'].idxmax()
print(
    "Newest content:",
    df.loc[newest_index, ['title', 'release_year']]
)
```
5) Perform Vectorized String Operations
```python
# Titles in uppercase
print(df['title'].str.upper())

# Titles in lowercase
print(df['title'].str.lower())

# Length of each title
print(df['title'].str.len())

# Containing Love
print(
    df.loc[
        df['title'].str.contains(
            'Love',
            na=False,
            case=False
        ),
        ['title']
    ]
)

# Containing Life
print(
    df.loc[
        df['title'].str.contains(
            'Life',
            na=False,
            case=False
        ),
        ['title']
    ]
)

# Starting with The
print(
    df.loc[
        df['title'].str.startswith(
            'The',
            na=False
        ),
        ['title']
    ]
)

# Ending with Story
print(
    df.loc[
        df['title'].str.endswith(
            'Story',
            na=False
        ),
        ['title']
    ]
)

# Extract first genre
df['Primary Genre'] = df['listed_in'].str.split(',').str[0]
print(df['Primary Genre'].head())

# Primary Genre
print(
    df[
        ['title', 'listed_in', 'Primary Genre']
    ].head()
)
```
6) Perform Aggregation and Grouping
```python
# Number of Movies and TV Shows
print(
    df.groupby('type')['title'].count()
)

# Average release year by content type
print(
    df.groupby('type')['release_year'].mean()
)

# Number of titles in each rating category
print(
    df.groupby('rating')['title'].count()
)
```
7) Perform Frequency Analysis
```python
# Top 10 countries
countries = (
    df['country']
    .dropna()
    .str.split(',')
    .explode()
    .str.strip()
)
print(
    countries.value_counts().head(10)
)

# Top 10 directors
directors = (
    df['director']
    .dropna()
    .str.split(',')
    .explode()
    .str.strip()
)
print(
    directors.value_counts().head(10)
)

# Top 10 genres
genres = (
    df['listed_in']
    .dropna()
    .str.split(',')
    .explode()
    .str.strip()
)
print(
    genres.value_counts().head(10)
)
```
8) Create Pivot Tables
```python
# Distribution of Movies and TV Shows across countries

country_data = df.assign(
    country=df['country'].str.split(',')
).explode('country')
country_data['country'] = (
    country_data['country'].str.strip()
)

pivot_country = country_data.pivot_table(
    index='country',
    columns='type',
    values='title',
    aggfunc='count',
    fill_value=0
)

print(pivot_country)


# Distribution by release year

pivot_year = df.pivot_table(
    index='release_year',
    columns='type',
    values='title',
    aggfunc='count',
    fill_value=0
)

print(pivot_year)
```
9) Perform Hierarchical Indexing (MultiIndex)
```python
# MultiIndex
multi_df = df.set_index(['country', 'type'])

# Movies from India
print(
    multi_df.loc['India', 'Movie']
)

# TV Shows from United States
print(
    multi_df.loc['United States', 'TV Show']
)
```
10) Combine DataFrames
```python
# Split dataset
movies = df[df['type'] == 'Movie']
tv_shows = df[df['type'] == 'TV Show']

# Concatenate
combined_df = pd.concat(
    [movies, tv_shows]
)
print(combined_df.head())

# Title and Director 
title_director = df[['title', 'director']]

# Title and Country 
title_country = df[['title', 'country']]

# Merge title column
merged_df = pd.merge(
    title_director,
    title_country,
    on='title'
)
print(merged_df.head())
```
11) Analyze Netflix Additions Over Time
```python
# Convert date_added
df['date_added'] = pd.to_datetime(
    df['date_added'],
    errors='coerce'
)

# Extract year
df['Year Added'] = df['date_added'].dt.year

# Extract month
df['Month Added'] = df['date_added'].dt.month_name()

# Display
print(
    df[
        [
            'title',
            'date_added',
            'Year Added',
            'Month Added'
        ]
    ].head()
)

# Number of Movies and TV Shows added each year
yearly_additions = (
    df.groupby(
        ['Year Added', 'type']
    )
    .size()
    .unstack(fill_value=0)
)

print(yearly_additions)

# Number of Movies and TV Shows added each month
monthly_additions = (
    df.groupby(
        ['Month Added', 'type']
    )
    .size()
    .unstack(fill_value=0)
)
print(monthly_additions)
```
12) Generate a Summary Report
```python
# Country with highest Netflix content

countries = (
    df['country']
    .dropna()
    .str.split(',')
    .explode()
    .str.strip()
)

top_country = countries.value_counts().idxmax()

print(
    "Country with highest Netflix content:",
    top_country
)

print(
    countries.value_counts().head(10)
)


# Year with highest no. of additions

year_counts = df['Year Added'].value_counts()

highest_year = year_counts.idxmax()
highest_count = year_counts.max()

print(
    "Year with highest additions:",
    highest_year
)

print(
    "Number of additions:",
    highest_count
)


# Most common rating

most_common_rating = (
    df['rating']
    .value_counts()
    .idxmax()
)

print(
    "Most common rating:",
    most_common_rating
)


# Most common genre

genres = (
    df['listed_in']
    .dropna()
    .str.split(',')
    .explode()
    .str.strip()
)

most_common_genre = (
    genres.value_counts().idxmax()
)

print(
    "Most common genre:",
    most_common_genre
)


# Percentage of Movies and TV Shows

type_counts = df['type'].value_counts()

type_percentage = (
    type_counts / len(df)
) * 100

print(type_percentage)

# Five meaningful observations
print(
    "Country with highest content:",
    countries.value_counts().idxmax()
)

print(
    "Year with highest additions:",
    df['Year Added'].value_counts().idxmax()
)

print(
    "Most common rating:",
    df['rating'].value_counts().idxmax()
)

print(
    "Most common genre:",
    genres.value_counts().idxmax()
)

print("Content percentage:")

print(
    (df['type'].value_counts() / len(df)) * 100
)
```

##Output
1.Load and Explore the Dataset

<img width="783" height="593" alt="image" src="https://github.com/user-attachments/assets/a5de45a2-16f7-4b7d-856d-b7cf68089cee" />
<img width="452" height="302" alt="image" src="https://github.com/user-attachments/assets/95ec9011-19a5-4686-a8f9-5d1ef81fda29" />

2. Analyze missing values

<img width="1788" height="757" alt="image" src="https://github.com/user-attachments/assets/46b8154f-503e-4d8d-ad90-1f9299c6ca95" />

3. Perform data indexing and selection

<img width="486" height="586" alt="image" src="https://github.com/user-attachments/assets/39c51478-7454-4970-b943-d236456829e3" />
<img width="577" height="667" alt="image" src="https://github.com/user-attachments/assets/7c0b7e94-f1a0-4560-bdc6-71ce4582eba0" />

4. Create new columns

<img width="935" height="766" alt="image" src="https://github.com/user-attachments/assets/8df45c9a-cffa-4ca8-b327-b414aa2225e0" />

5. Perform vectorized string operations

<img width="611" height="655" alt="image" src="https://github.com/user-attachments/assets/81183d13-1857-48f2-9f6f-f15befec4a84" />
<img width="526" height="197" alt="image" src="https://github.com/user-attachments/assets/c0a72bca-5e16-49e8-9e27-23260c030a27" />

6. Perform aggregation and grouping

<img width="496" height="386" alt="image" src="https://github.com/user-attachments/assets/5241c7e9-e0c8-4e16-8a13-e1e27121703d" />

7. Perform frequency analysis

<img width="466" height="532" alt="image" src="https://github.com/user-attachments/assets/26c37d8b-6ecd-47d8-b3a9-6ce9ccbd0713" />

8. Create pivot tables

<img width="586" height="451" alt="image" src="https://github.com/user-attachments/assets/e85eced1-161f-4e41-b63f-3670aad2e4c1" />

9. Perform hierarchical indexing (multiindex)

<img width="833" height="637" alt="image" src="https://github.com/user-attachments/assets/5ba8d35e-7a9c-496e-9e26-150e175ac725" />

10. Combine DataFrames

<img width="778" height="763" alt="image" src="https://github.com/user-attachments/assets/a779d28d-8509-4907-a784-d6756bf1072b" />

11. Analyze netflix additions over time

<img width="650" height="522" alt="image" src="https://github.com/user-attachments/assets/aefee73a-ef83-45b0-91fb-52a36a412dab" />

12. Generate a summary report

<img width="487" height="496" alt="image" src="https://github.com/user-attachments/assets/a6d77474-6991-45d0-affe-ca123b5c5651" />

**Result**
Helps Netflix in content planning & investments.
