# MoviesDataAnalysis-using-R
Data analysis using Rstudio cloud 
---
title: "Movies Data Analysis in R"
author: "Sonia Dalal"
date: "`r Sys.Date()`"
output:
  html_document:
    df_print: paged
    css: style.css
  pdf_document: default
editor_options:
  markdown:
    wrap: 72
---


## Project Overview

# Introduction

This R project analyzes a movie dataset to explore patterns in revenue, genres, budgets, and directorial performance. It demonstrates how data analysis and visualization in R can uncover valuable business and entertainment insights.

# Project Goal

** Clean and transform raw movie data for analysis.

** Summarize results to identify high-performing directors and genres.

** Investigate revenue trends by genre and director.

** Create meaningful visualizations using ggplot2.

# Key Questions

** Which movie genres generate the most revenue?

** Which directors have directed the most movies?

** What is the total revenue per director?

** Are there outliers in the revenue data?

** Identify the top 10 movies in terms of revenue?

** How does revenue relate to profit and budget?

#  packages and library you need before beginning the R project:
Firstly To install the tidyverse — a collection of essential R packages for data science — run the following command in your R console or RStudio:
Packages	Purpose:

Installing tidyverse automatically installs core packages such as:

ggplot2 – for data visualization

dplyr – for data manipulation

tidyr – for data tidying

readr – for reading data (CSV, TSV, etc.)

purrr – for functional programming

tibble – for enhanced data frames

stringr – for string manipulation

forcats – for working with categorical variables

# Package Installation and Loading
Code to install different packages:

install.packages("tidyverse")
install.packages("skimr")
install.packages("stringr")
install.packages("readr")
install.packages("ggplot2")

library(tidyverse)
library(skimr)
library(stringr)
library(readr)
library(ggplot2)


```{r}
install.packages("tidyverse")
```

#Load it in your script or RMarkdown using below code:

```{r}
library(tidyverse)
```

#dplyr	For data manipulation (filtering, grouping, summarising, etc.)
#ggplot2	For creating visualizations like bar plots, boxplots, etc.
#readr	For reading CSV files efficiently
#tidyr	For reshaping and cleaning data
#stringr	For string/text operations (e.g., lowercase conversion)
#forcats	For handling categorical variables (factors)
#readxl	(Optional) If working with Excel files
#skimr or summarytools	(Optional) For quick summary statistics

```{r}
install.packages("dplyr")
```

```{r}
library(dplyr)
```

```{r}
install.packages("tidyr")
```

```{r}
library(tidyr)
```

```{r}
library(ggplot2)
```

```{r}
library(skimr)
```

```{r}
install.packages("readr") 
```

```{r}
library(readr) 
```

```{r}
install.packages("stringr")
```

```{r}
library(stringr)
```

- **Load the Data:**
    - Load your data from a CSV file using `read.csv()`.
- **Explore the Data:**
    - Use `head()`, `summary()`, and `str()` to understand the data.
- **Handle Missing Values:**
    - Remove missing values using `na.omit()`.
    - Alternatively, impute missing values with mean or median using `mean()` or `median()`.
- **Remove Duplicates:**
    - Use `duplicated()` to remove duplicate rows.
- **Fix Column Names:**
    - Rename columns to avoid spaces using `gsub()`.
- **Filter Irrelevant Data:**
    - Use `filter()` to remove unnecessary rows or columns.
- **Transform Data:**
    - Convert columns to appropriate types using `as.numeric()`, `as.factor()`, etc.
    - Create new columns using `mutate()`.
- **Handle Outliers:**
    - Identify outliers using boxplots or scatter plots and remove them.
- **Standardize Categorical Variables:**
    - Ensure consistent naming for categorical data using `recode()`.
- **Save Cleaned Data:**
    - Save the cleaned data to a new file using `write.csv()`.
    
# Load Dataset

 \*\*Read the Data File (movies_data.csv) If you have the Movies.csv file
    uploaded or placed in a directory, you can use the read.csv function
    to load it into R. \# Read and load the data (movies_data.csv file) into
    a data frame

Read and load the data (movies_data.csv file) into a data frame

```{r}
movies_data <-
read.csv("/cloud/project/movies_data.csv")
```

# Initial Exploration

Steps to data Cleaning first of all explore the data using head(), summary(), and str()

View the first few rows of the data 
```{r}
head(movies_data) 
```
```{r}
summary(movies_data)
```
```{r}
str(movies_data)
```
#Remove duplicates
```{r}
movies_data <- movies_data[!duplicated(movies_data), ]
```

# Handle missing values: Remove or impute with mean/median

```{r}
colSums(is.na(movies_data))
```
This tells us how many missing values are in each column.

# Fill missing revenue with column mean
```{r}
movies_data$Revenue[is.na(movies_data$Revenue)] <- mean(movies_data$Revenue, na.rm = TRUE)
```

#Create a new column calculated profit

```{r}
movies_data_profit <- movies_data %>%
mutate(Profit = Revenue - Budget)
```

now lets view the impact again of above code running on the next code

#Convert Genre to factor and standardize
Under the Genre column- text action is changed to Action and drama is changed to Drama
```{r}
movies_data_clean <- movies_data %>%
  mutate(Genre = tolower(Genre)) %>%
  mutate(Genre = recode(Genre, "action" = "Action", "drama" = "Drama"))
```

#Transform and Format Data
This step involves transforming columns into a consistent format, such as converting data types or extracting certain values.

Convert a column to a factor (for categorical data)

```{r}
movies_data_clean$Genre <- as.factor(movies_data_clean$Genre)
```

Create a new column by extracting year from a release date column
```{r}
movies_data_cleanYear <- format(as.Date(movies_data_clean$Release_Date), "%Y")
```

# Standardizing categorical variables (e.g., correcting misspellings or uniform formatting)

Standardize text data (e.g., ensure consistency in genre naming)
```{r}
movies_data_clean$Genre <- tolower(movies_data_clean$Genre)
movies_data_clean$Genre <- recode(movies_data_clean$Genre, "action" = "Action", "drama" = "Drama")
```


# Save Cleaned Data to a new csv file

to get address of your current directory write code 
```{r}
getwd()
```
If you’re working inside an RStudio Project (e.g., Rprojects), and you want to save the cleaned data file in the main directory of the project, use:

```{r}
write.csv(movies_data_clean, "/cloud/project/cleaned_movies_data.csv", row.names = FALSE)
```

# Summary Analysis by Director

```{r}
summary_data <- movies_data %>%
    group_by(`Director._1_`) %>%
   summarise(
     Movies_count = n(),
     Total_Revenue = sum(Revenue, na.rm = TRUE)
   ) %>%
  arrange(desc(Total_Revenue))
```

Detailed Explanation:
group_by(Director._1_):

This tells R to group the data based on the unique values in the Director._1_ column. That means the dataset is divided into smaller groups where each group corresponds to a specific director.

summarise(Movies_count = n(), Total_Revenue = sum(Revenue, na.rm = TRUE)):

n(): This counts the number of rows (movies) in each group (for each director), effectively giving the number of movies that each director has in the dataset.

sum(Revenue, na.rm = TRUE): This calculates the total revenue for each director by summing up the Revenue column of all the movies directed by that director. The argument na.rm = TRUE ensures that if there are any missing values (NA) in the Revenue column, they are ignored during the summing process.

arrange(desc(Total_Revenue)):

After the summary data has been calculated, this step sorts the data in descending order based on the Total_Revenue. So the director with the highest total revenue will appear at the top of the list.


# Summary by Director Pairs

```{r}
summary_data <- movies_data_clean %>%
  group_by(`Director._1_`, `Director._2_`) %>%
  summarise(
    Movies_count = n(),
    Total_Revenue = sum(Revenue, na.rm = TRUE)
  ) %>%
  arrange(desc(Total_Revenue))
```

Explanation of Changes:
group_by(Director._1_, Director._2_):

This groups the data by both Director._1_ and Director._2_. This means that for every unique combination of Director._1_ and Director._2_, the summary will be calculated.

summarise(Movies_count = n(), Total_Revenue = sum(Revenue, na.rm = TRUE)):

The same summarization is applied: counting the number of movies and summing the revenue for each combination of directors.

arrange(desc(Total_Revenue)):

After grouping and summarizing, the data is sorted in descending order of Total_Revenue, so the most profitable director pairs appear first.


# Data visualization using ggplot**

# Visualize the distribution of the 'Revenue' column(boxplot)

```{r}
boxplot(movies_data_clean$Revenue)
```

** Distribution of Revenue by each Genre
This plot shows the distribution of revenue for each genre using a boxplot.

```{r}
ggplot(movies_data_clean, aes(x = Genre, y = Revenue)) +
geom_boxplot(aes(fill = Genre), color = "blue") +
labs(title = "Distribution of Revenue by Genre", x = "Genre", y = "Revenue") +
theme_minimal()
```
Explanation:
ggplot(movies_data_clean, aes(x = Genre, y = Revenue)):

ggplot(): This is the main function from the ggplot2 package that initializes a plot.

movies_data_clean: This is the data frame you're using for the plot. It contains the cleaned data of movies.

aes(x = Genre, y = Revenue): This part defines the aesthetic mappings for the plot. It means:

x = Genre: The Genre column will be plotted along the x-axis.

y = Revenue: The Revenue column will be plotted along the y-axis.

geom_boxplot(aes(fill = Genre), color = "blue"):

geom_boxplot(): This function creates a box plot, which is useful for visualizing the distribution of data and identifying outliers.

A box plot shows the minimum, first quartile (Q1), median, third quartile (Q3), and maximum of a numerical variable (in this case, Revenue).

Outliers are often shown as points beyond the "whiskers" of the box plot.

aes(fill = Genre): This part is used to color the boxes in the plot based on the Genre column. Different genres will have different colors.

color = "blue": This sets the border color of the box plots to blue.

labs(title = "Distribution of Revenue by Genre", x = "Genre", y = "Revenue"):

labs(): This function is used to add labels to the plot.

title = "Distribution of Revenue by Genre": This adds the title of the plot at the top.

x = "Genre": This labels the x-axis with "Genre".

y = "Revenue": This labels the y-axis with "Revenue".

theme_minimal():

theme_minimal(): This applies a minimal theme to the plot, which reduces the clutter and focuses on the data. It removes background grids and other elements to make the plot cleaner and easier to read.

# Revenue Distribution by Genre (Histogram)
You can visualize the distribution of revenue within each genre using a histogram. It will show how revenues are spread out and if some genres tend to have higher earnings.

```{r}
ggplot(movies_data_clean, aes(x = Revenue, fill = Genre)) +
  geom_histogram(bins = 30, color = "blue", alpha = 0.8) +
  labs(title = "Revenue Distribution by Genre", x = "Revenue", y = "Count") +
  theme_minimal() +
  facet_wrap(~ Genre, scales = "free_y")
```

# Revenue Distribution by Genre (Violin Plot)
A violin plot can show the distribution of revenues for each genre and how they compare in terms of spread and central tendency.

```{r}
ggplot(movies_data_clean, aes(x = Genre, y = Revenue, fill = Genre)) +
  geom_violin() +
  labs(title = "Revenue Distribution by Genre", x = "Genre", y = "Revenue") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```
# Top 10 Movies by Revenue (Bar Chart)
You can identify the top 10 movies in terms of revenue, which could give additional insights into the biggest blockbusters.

```{r}
top_movies <- movies_data_clean %>%
  arrange(desc(Revenue)) %>%
  head(10)

ggplot(top_movies, aes(x = reorder(Movie.Title, Revenue), y = Revenue, fill = Genre)) +
  geom_bar(stat = "identity") +
  coord_flip() +
  labs(title = "Top 10 Movies by Revenue", x = "Movie", y = "Revenue") +
  theme_minimal()
```
# Revenue vs. Profit (Bubble Chart)
Show the relationship between Revenue and Profit using a bubble chart. The size of the bubble can represent the budget of the movie.


```{r}
movies_data_clean <- movies_data_clean %>%
  mutate(Profit = Revenue - Budget)
```

```{r}
ggplot(movies_data_clean, aes(x = Revenue, y = Profit, size = Budget, color = Genre)) +
  geom_point(alpha = 0.6) +
  labs(title = "Revenue vs. Profit by Genre", x = "Revenue", y = "Profit") +
  scale_size_continuous(range = c(3, 15)) +
  theme_minimal()
```

# Bubble Chart: Budget vs Revenue with Profit as Bubble Size


```{r}
ggplot(movies_data_clean, aes(x = Budget, y = Revenue, size = Profit, color = Genre)) +
  geom_point(alpha = 0.6) +
  scale_size_continuous(range = c(2, 10)) +  # Adjust bubble sizes
  labs(
    title = "Bubble Chart: Budget vs Revenue (Bubble = Profit)",
    x = "Budget",
    y = "Revenue",
    size = "Profit"
  ) +
  theme_minimal()
```

** Explanation:
x = Budget, y = Revenue: X and Y axes.

size = Profit: Bubble size shows how profitable each movie was.

color = Genre: Helps differentiate genres visually.

alpha = 0.6: Makes bubbles semi-transparent so overlaps are easier to interpret.

scale_size_continuous(): Controls bubble size scaling.

# Conclusion

This project successfully achieved its goal of analyzing a movie dataset to uncover insights related to revenue, genre trends, budgeting, and directorial performance. By systematically cleaning and transforming the raw data, the analysis provided a reliable foundation for visual exploration and statistical summaries.

** Key findings include:

Action and Drama emerged as the most profitable genres in terms of average revenue.

Certain directors consistently outperformed others in terms of both the number of films directed and total box office earnings.

A clear positive correlation was observed between budget and revenue, though a few outliers highlighted the impact of marketing, genre appeal, or star power.

The creation of the Profit metric revealed that high revenue doesn’t always equate to high profitability.

Through the use of dplyr for data manipulation and ggplot2 for visualization, this analysis demonstrates how R can be leveraged to generate data-driven insights in the entertainment industry. These insights could help production houses make more informed decisions about genre investments, budget allocations, and director collaborations.


