# LEGO Data Analysis

This project dives deep into a LEGO dataset to explore various aspects of the company's history, product offerings, and trends over time. We'll answer interesting questions such as:

- What is the most enormous LEGO set ever created, and how many parts did it have?
- How did the LEGO company start out? In which year were the first LEGO sets released, and how many sets did the company sell when it first launched?
- Which LEGO theme has the most sets? Is it one of LEGO's own themes like Ninjago, or a theme they licensed like Harry Potter or Marvel Superheroes?
- When did the LEGO company really expand its product offering? Can we spot a change in the company's strategy based on how many themes and sets were released year-on-year?
- Did LEGO sets grow in size and complexity over time? Do older LEGO sets tend to have more or fewer parts than newer sets?

## Data Source

The dataset used in this project is sourced from [Rebrickable](https://rebrickable.com/downloads/), which has compiled data on all LEGO pieces in existence. This project relies on .csv files that can be downloaded from the Rebrickable website.

![LEGO](https://i.imgur.com/49FNOHj.jpg)

## Data Exploration

### Challenge 1: Counting LEGO Colors

We start by exploring the `colors.csv` file to understand how many different colors LEGO produces.

```python
import pandas as pd
import matplotlib.pyplot as plt

colors_df = pd.read_csv('data/colors.csv')
colors_df.head(10)
colors_df.nunique()
```

### Challenge 2: Transparent vs. Opaque Colors

Next, we compare the number of transparent colors (`is_trans == 't'`) to the number of opaque colors (`is_trans == 'f'`).

```python
colors_df.groupby('is_trans').count()
colors_df.is_trans.value_counts()
```

### Understanding LEGO Themes vs. LEGO Sets

Walk into a LEGO store, and you will see their products organized by theme. Their themes include Star Wars, Batman, Harry Potter, and many more.

![LEGO Themes](https://i.imgur.com/aKcwkSx.png)

A LEGO set is a particular box of LEGO or product. Therefore, a single theme typically has many different sets.

![LEGO Sets](https://i.imgur.com/whB1olq.png)

### Challenge 3: Exploring LEGO Sets Data

The `sets.csv` file contains a list of sets over the years and the number of parts that each set contained.

```python
sets_df = pd.read_csv('data/sets.csv')
sets_df.head()
sets_df.tail()
```

### Challenge 4: The First LEGO Sets

We explore which year the first LEGO sets were released and what these sets were called.

```python
sets_df[['name','year']].loc[sets_df.year.idxmin()]
```

### Challenge 5: LEGO Sets in the First Year

We look at how many different sets LEGO sold in their first year and how many types of LEGO products were on offer.

```python
sets_df[sets_df['year'] == 1949]
```

### Challenge 6: The Top 5 Largest LEGO Sets

We find the top 5 LEGO sets with the most number of parts.

```python
sets_df.sort_values('num_parts',ascending=False).head()
```

### Challenge 7: LEGO Sets Released Year-on-Year

Using `.groupby()` and `.count()`, we show the number of LEGO sets released each year.

```python
sets_by_year = sets_df.groupby('year').count()
sets_by_year['set_num'].head()
```

We also visualize the number of LEGO releases on a line chart using Matplotlib.

```python
plt.plot(sets_by_year.index ,sets_by_year.set_num)
plt.plot(sets_by_year.index[:-2] ,sets_by_year.set_num[:-2])
```

### Challenge 8: Number of Themes Shipped by Year

We calculate the number of different themes shipped by year by counting the number of unique `theme_id`s per calendar year.

```python
themes_by_year = sets_df.groupby('year').agg({'theme_id': pd.Series.nunique})
themes_by_year.rename(columns={'theme_id':'nr_themes'}, inplace=True)
themes_by_year.head()
themes_by_year.tail()
```

We then plot the number of themes released by year.

```python
plt.plot(themes_by_year.index[:-2], themes_by_year.nr_themes[:-2])
```

### Challenge 9: Line Charts with Two Separate Axes

We create a plot with two separate axes to compare the number of sets released each year with the number of themes.

```python
ax1 = plt.gca()
ax2 = ax1.twinx()

ax1.plot(sets_by_year.index[:-2] ,sets_by_year.set_num[:-2], color='g')
ax2.plot(themes_by_year.index[:-2], themes_by_year.nr_themes[:-2], color='b')

ax1.set_xlabel('Year')
ax1.set_ylabel('Number of sets', color='green')
ax2.set_ylabel('Number of Themes', color='blue')
```

### Challenge 10: Average Number of Parts per Set

Using `.groupby()` and `.agg()`, we calculate the average number of parts per set.

```python
parts_per_set = sets_df.groupby('year').agg({'num_parts':pd.Series.mean})
parts_per_set.head()
```

### Challenge 11: LEGO Set Size Over Time

We plot the average number of parts over time using a scatter plot to see if LEGO sets have increased in size and complexity over time.

```python
plt.scatter(parts_per_set.index[:-2], parts_per_set.num_parts[:-2])
```

### Challenge 12: Number of Sets per LEGO Theme

We explore which LEGO theme has the largest number of individual sets.

```python
set_theme_count = sets_df['theme_id'].value_counts()
set_theme_count.head()
```

### Database Schemas, Foreign Keys, and Merging DataFrames

We explore the themes data and learn how to merge DataFrames based on a key.

```python
themes = pd.read_csv('data/themes.csv')
themes.head(10)

themes[themes['name'] == 'Star Wars']
sets_df[sets_df['theme_id'] == 18]
sets_df[sets_df['theme_id'] == 209]

set_theme_count = pd.DataFrame({
    'id':set_theme_count.index,
    'set_count':set_theme_count.values
    })
merged_df = pd.merge(set_theme_count, themes, on='id')
merged_df[:3]
```

Finally, we create a bar chart to visualize the number of sets per LEGO theme.

```python
plt.figure(figsize=(14,8))
plt.xticks(fontsize=14,rotation=90)
plt.yticks(fontsize=14)
plt.ylabel('Nr of sets', fontsize=14)
plt.xlabel('Theme Name', fontsize=14)
plt.bar(merged_df.name[:10], merged_df.set_count[:10])
```

## Conclusion

This project provided insights into the LEGO company's evolution over time, revealing trends in product offerings, set sizes, and the growth of themes. By analyzing the dataset, we uncovered the history and expansion of LEGO sets and themes, showing how the company has grown and adapted to changing market demands.

## License

This project is licensed under the MIT License.

## Acknowledgements

- The dataset was sourced from [Rebrickable](https://rebrickable.com/downloads/).
- The project structure and inspiration were derived from various data analysis tutorials.

## Author

- [Prathamesh](https://github.com/Prathamesh326)
