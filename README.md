import pandas as pd

# Load dataset
df = pd.read_csv('netflix_titles.csv')

# Fill missing values
df.fillna({
    'director': 'Unknown',
    'cast': 'Unknown',
    'country': 'Unknown',
    'date_added': 'Unknown',
    'rating': 'Unknown',
    'duration': '0 Unknown'
}, inplace=True)

# Remove duplicates
df.drop_duplicates(inplace=True)

# Convert date
df['date_added'] = pd.to_datetime(df['date_added'], errors='coerce')

# Standardize title casing
df['title'] = df['title'].str.title().str.strip()
df['director'] = df['director'].str.title().str.strip()

# Clean and standardize 'country' field
def clean_country(countries):
    return ', '.join(sorted([country.strip().title() for country in str(countries).split(',')]))

df['country'] = df['country'].apply(clean_country)

# Clean and standardize 'listed_in' (genres)
def clean_genres(genres):
    return ', '.join(sorted(set([g.strip().title() for g in str(genres).split(',')])))

df['listed_in'] = df['listed_in'].apply(clean_genres)

# Extract duration value and unit
df[['duration_int', 'duration_unit']] = df['duration'].str.extract(r'(\d+)\s*(\w+)')
df['duration_int'] = pd.to_numeric(df['duration_int'], errors='coerce')
