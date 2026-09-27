# **Practical 2 - Accessing and Manipulating Composite Data**

---

## Introduction

In Practical 1, we worked with atomic vectors, which store one-dimensional sequences of elements of the same data type. However, real scientific datasets and complex computational models require **composite data structures** capable of organizing multi-dimensional, multi-variable, or heterogeneous information:

1. **Matrices:** Two-dimensional grids of values of a single atomic type (e.g., spatial grids, transition tables).
2. **Lists:** Ordered, flexible collections where each element can be an entirely different type, length, or structure (e.g., packaging diverse outputs or survey records).
3. **Data Frames:** The standard tabular structure for statistical analysis in R, where rows represent observations and columns represent different variables (which can be of different types).

In this practical, you will learn to construct, inspect, subset, modify, and aggregate each of these composite structures. Mastering these techniques is directly required for Challenge 1 (nested list processing) and Challenge 2 (data frame simulation and filtering).

---

## Matrices: Two-Dimensional Grids

A **matrix** is a two-dimensional rectangular array organized into rows and columns. Like an atomic vector, every cell in a matrix must contain the exact same data class (numeric, character, or logical).

### Creating Matrices

Matrices can be constructed using `matrix()`, or by binding vectors together using `rbind()` (row bind) and `cbind()` (column bind):

```R
# Creating a matrix from a sequence:
# By default, R fills matrices column-by-column:
mat1 <- matrix(data = 1:12, nrow = 4, ncol = 3)
mat1
#      [,1] [,2] [,3]
# [1,]    1    5    9
# [2,]    2    6   10
# [3,]    3    7   11
# [4,]    4    8   12

# Fill row-by-row instead:
mat2 <- matrix(data = 1:12, nrow = 4, ncol = 3, byrow = TRUE)
mat2
#      [,1] [,2] [,3]
# [1,]    1    2    3
# [2,]    4    5    6
# [3,]    7    8    9
# [4,]   10   11   12
```

We can also bind vectors of equal length:
```R
site_a <- c(2.1, 4.5, 3.2)
site_b <- c(1.8, 4.1, 3.9)

# Bind as rows:
row_bound <- rbind(site_a, site_b)  # 2 rows, 3 columns

# Bind as columns:
col_bound <- cbind(site_a, site_b)  # 3 rows, 2 columns
```

### Inspecting Matrix Dimensions and Naming

To check the dimensions of a matrix:
* `dim(m)`: Returns a vector `c(nrow, ncol)`.
* `nrow(m)`: Number of rows.
* `ncol(m)`: Number of columns.

You can assign and query names for rows and columns:
```R
colnames(col_bound) <- c("Transect_1", "Transect_2")
rownames(col_bound) <- c("Depth_1m", "Depth_2m", "Depth_3m")
col_bound
```

### Matrix Subsetting and Slicing

Subsetting a matrix requires two indices inside square brackets: `[row, column]`. Remember the mnemonic: **R**ow first, **C**olumn second (**RC**).

* `mat[r, c]`: Element at row `r`, column `c`.
* `mat[r, ]`: **Entire row `r`** (leaving column blank extracts all columns).
* `mat[, c]`: **Entire column `c`** (leaving row blank extracts all rows).
* `mat[c(1, 3), c(2, 3)]`: Sub-grid spanning rows 1 & 3 and columns 2 & 3.

```R
# Using mat1 from above:
mat1[2, 3]        # 10
mat1[1:2, ]       # First two rows across all columns
mat1[, 2]         # Entire second column: 5 6 7 8
```

> **Note on Dimension Dropping:** When you extract a single row or single column (e.g., `mat1[, 2]`), R automatically drops the matrix dimensions and returns a simple 1D vector. If you wish to preserve the matrix structure, add the argument `drop = FALSE`:
> ```R
> mat1[, 2, drop = FALSE]  # Returns a 4x1 matrix
> ```

### Matrix Arithmetic and Margins

Mathematical operations on matrices occur element-by-element:
```R
mat1 * 10
mat1 + mat2
```

To summarize rows or columns, use R's fast built-in margin functions:
* `rowSums(m)`, `colSums(m)`: Sum totals across rows or columns.
* `rowMeans(m)`, `colMeans(m)`: Averages across rows or columns.

```R
colMeans(mat1)  # 2.5 6.5 10.5
```

#### Q1. Consider the matrix:
```R
grid <- matrix(data = seq(2, 36, by = 2), nrow = 6, ncol = 3)
```
Without running the code first:
1. How many total elements are in `grid`?
2. What value is located at `grid[3, 2]`?
3. Write an expression to replace all values in `grid` that are greater than 20 with `20`.

---

## Lists: Heterogeneous and Hierarchical Data

An atomic vector and a matrix are restricted to holding elements of one class. A **list**, however, is a generic vector that can contain elements of **any class, any length, and any structure** — including vectors of different lengths, matrices, data frames, or even other nested lists.

Lists are widely used in R to bundle related parameters, package complex simulation results, or return grouped records.

### Creating Lists

Lists are constructed with the `list()` function. It is strongly recommended to give every element a descriptive name:

```R
survey_site <- list(
  town_name       = "Norwich",
  sample_date     = "2026-05-14",
  quadrat_counts  = c(12, 18, 14, 22),
  certified_clean = TRUE,
  soil_ph_readings = c(5.2, 5.4, 5.1)
)
```

In the Environment tab of RStudio, click the blue expand arrow next to `survey_site` to inspect its hierarchical structure.

### Accessing List Elements: `[` vs `[[` vs `$`

Understanding the distinction between single brackets `[` and double brackets `[[` is critical when working with lists:

* **Single Brackets `[ ]` (Sub-listing):**
  Extracts a *slice* of the list. **It always returns a list**, even if you ask for only one element:
  ```R
  sub <- survey_site["town_name"]
  class(sub)  # "list"
  ```
  *Analogy:* Imagine a list as a train with cargo wagons. `survey_site[1]` uncouples the 1st wagon and hands you a shorter train consisting of that single wagon.

* **Double Brackets `[[ ]]` (Element Extraction):**
  Extracts the *actual object stored inside* the designated position or name:
  ```R
  name_val <- survey_site[["town_name"]]
  class(name_val)  # "character"
  ```
  *Analogy:* `survey_site[[1]]` opens up the 1st wagon, unpacks the cargo inside, and hands you the cargo directly.

* **Dollar Sign `$` (Named Extraction):**
  Shorthand for `[[ "name" ]]`:
  ```R
  survey_site$quadrat_counts  # Returns the numeric vector c(12, 18, 14, 22)
  ```

```R
# You can immediately apply vector operations to extracted elements:
mean(survey_site$quadrat_counts)  # 16.5
```

### Modifying and Adding List Elements

You can modify existing elements or add brand new elements by assigning directly to a new name:

```R
# Modify existing element:
survey_site$certified_clean <- FALSE

# Add a new element:
survey_site$surveyor <- "Dr. Smith"

# Remove an element by assigning NULL:
survey_site$sample_date <- NULL
```

### Nested Lists

Because list elements can be anything, you can nest lists inside lists. This is precisely how multi-town market reports or hierarchical biological models are represented:

```R
market_report <- list(
  Norwich = list(honey = 12.5, pumpkins = 45, organic = TRUE,  expenses = 120.5),
  Diss    = list(honey = 4.0,  pumpkins = 18, organic = FALSE, expenses = 45.0),
  Holt    = list(honey = 8.2,  pumpkins = 30, organic = TRUE,  expenses = 82.3)
)

# Extract information for a specific town:
market_report[["Norwich"]]$honey        # 12.5
market_report$Holt$organic              # TRUE
```

#### Q2. Given `market_report` above:
1. Write an expression to retrieve the number of pumpkins bought in `Diss`.
2. Add a new town named `"Aylsham"` to `market_report` containing 10.0 kg of honey, 25 pumpkins, organic certification `TRUE`, and expenses of £70.0.
3. Calculate the total honey purchased across all organic towns in `market_report` (Norwich, Holt, Aylsham).

---

## Data Frames: The Workhorse of Data Analysis

A **data frame** is a two-dimensional table designed specifically for data analysis. Formally, a data frame is a named list of vectors of **identical length**, where:
* Each **column** represents a variable (and must be of a single atomic class).
* Each **row** represents an observation (and can span multiple classes across columns).

Unlike a matrix (which forces all cells into one type), a data frame can freely hold numeric columns, character columns, and logical columns side by side.

### Creating Data Frames

Construct data frames using `data.frame()`:

```R
survey_df <- data.frame(
  plot_id    = 1:6,
  soil_ph    = c(4.8, 6.2, 5.1, 7.4, 4.9, 5.3),
  tree_count = c(18,  10,  16,   7,  22,  15),
  vegetation = c("Pine", "Oak", "Pine", "Oak", "Pine", "Pine"),
  is_wetland = c(TRUE, FALSE, FALSE, FALSE, TRUE, FALSE),
  stringsAsFactors = FALSE
)

survey_df
```

Inspect the structure and dimensions of your data frame:
```R
dim(survey_df)      # 6 rows, 5 columns
nrow(survey_df)     # 6
ncol(survey_df)     # 5
names(survey_df)    # Column names
str(survey_df)      # Structure overview
head(survey_df, 3)  # First 3 rows
```

### Accessing Columns and Cells

Columns can be extracted as vectors using `$`, `[[ ]]`, or `[, "column_name"]`:

```R
# Extract a column as a vector:
survey_df$soil_ph
survey_df[["soil_ph"]]
survey_df[, "soil_ph"]

# Extract a specific cell: row 3, column 'soil_ph':
survey_df[3, "soil_ph"]  # 5.1
```

### Adding and Computing New Columns

You can add new variables directly or compute them from existing columns. This is a foundational step in simulation modelling:

```R
# In Challenge 2, biomass is calculated as:
# tree_count * (2.0 + 0.5 * soil_ph)

survey_df$biomass <- survey_df$tree_count * (2.0 + 0.5 * survey_df$soil_ph)

# View updated data frame with new biomass column:
survey_df
```

---

## Subsetting and Filtering Data Frames

Data analysis routinely requires filtering data frames to isolate observations meeting precise environmental or experimental criteria.

### 1. Filtering with Row Logical Indexing

The standard and most robust method to filter a data frame is passing a logical condition into the **row index position**: `df[condition, ]`.

```R
# Find plots where soil pH is strictly less than 5.5:
acidic_plots <- survey_df[survey_df$soil_ph < 5.5, ]
acidic_plots

# Combine multiple conditions:
# Soil pH < 5.5 AND tree count >= 15:
specialist_df <- survey_df[survey_df$soil_ph < 5.5 & survey_df$tree_count >= 15, ]
specialist_df
```

> **Crucial Syntax Reminder:** Do not forget the comma inside the brackets! 
> * `survey_df[condition, ]` filters **rows** and keeps all columns.
> * `survey_df[condition]` would attempt to subset columns by name or index and cause errors.

### 2. Filtering with `subset()`

R also provides the convenient helper function `subset()`, which evaluates column names without repeatedly typing `survey_df$`:

```R
specialist_sub <- subset(survey_df, soil_ph < 5.5 & tree_count >= 15)
```

While `subset()` is clean for interactive exploration, standard bracket indexing `df[condition, ]` is preferred inside scripted functions and simulation packages because of predictable evaluation rules.

---

## Aggregation and Handling Boundary Cases

Once a data frame is filtered, we often compute summary statistics on specific columns.

```R
# Calculate the mean biomass of the specialist plots:
mean_spec_biomass <- mean(specialist_df$biomass)
mean_spec_biomass
```

### Boundary Case: Zero Qualifying Rows

In simulations with stochastic conditions, it is entirely possible that **no observations qualify** under the filtering criteria. Your code must handle this gracefully without crashing:

```R
# Suppose we search for extreme plots that do not exist in our data:
extreme_df <- survey_df[survey_df$soil_ph < 3.5 & survey_df$tree_count > 50, ]

nrow(extreme_df)  # Returns 0
```

If you attempt to compute the mean of an empty numeric vector:
```R
mean(extreme_df$biomass)  # Returns NaN (Not a Number)
```

In Challenge 2, if no quadrats meet the specialist criteria, your function must return `NA` as a single scalar. You can verify whether a filtered data frame has observations by checking `nrow(df) > 0`:

```R
result <- if (nrow(extreme_df) > 0) mean(extreme_df$biomass) else NA
result  # Returns NA
```
*(We will formalize `if`-`else` syntax in Practical 3, but note how `nrow()` enables boundary detection).*

---

## Missing Data in Data Frames

Tabular datasets often contain missing values (`NA`). 

```R
messy_survey <- data.frame(
  plot_id = 1:4,
  ph      = c(5.1, NA, 6.4, 4.9),
  trees   = c(12, 15, NA, 20)
)
```

* `is.na(messy_survey)`: Returns a logical matrix indicating missing cells across the table.
* `complete.cases(messy_survey)`: Returns a logical vector of length `nrow`, which is `TRUE` only for rows that have **no missing values in any column**.
* `na.omit(messy_survey)`: Returns a clean data frame with all rows containing any `NA` stripped out.

```R
complete.cases(messy_survey)  # TRUE FALSE FALSE  TRUE

clean_survey <- messy_survey[complete.cases(messy_survey), ]
clean_survey
#   plot_id  ph trees
# 1       1 5.1    12
# 4       4 4.9    20
```

#### Q3. In `survey_df` from Section 4, write code to:
1. Filter the data frame to select all plots where `vegetation == "Pine"` and `biomass > 60`.
2. Extract the `soil_ph` values of these qualifying plots as a plain numeric vector.
3. Compute the median pH of these qualifying plots.

---

## Comprehensive Review Exercises

These exercises combine matrices, lists, and data frames into workflows mirroring your upcoming challenges.

### Exercise 1: Building a Structured Market Database (Challenge 1 Prep)

Imagine you receive five parallel vectors describing purchases from regional agricultural markets:
```R
towns       <- c("Norwich", "Diss", "Aylsham", "Holt")
honey_kg    <- c(25.0, 10.5, 18.0, 4.2)
pumpkins    <- c(80, 35, 60, 15)
all_organic <- c(TRUE, FALSE, TRUE, FALSE)
expenses    <- c(240.0, 95.0, 175.5, 42.0)
```

1. Convert these parallel vectors into a single data frame named `market_df` with appropriate column names.
2. In Challenge 1, each honey-roasted pumpkin meal requires:
   * $0.022\text{ kg}$ ($22\text{ g}$) of honey.
   * Exactly $1$ pumpkin.
   
   For each market in `market_df`, calculate how many complete meals could potentially be made from its honey alone (`floor(honey_kg / 0.022)`), and from its pumpkins alone. The meal capacity of that market is the smaller of these two numbers (hint: use `pmin()`, which computes parallel element-wise minimums across vectors). Add this as a column named `meal_capacity`.
3. Construct a named list `market_list` containing 4 elements, where the names of the list elements are the town names (`Norwich`, `Diss`, etc.). Each element must itself be a list containing the purchase details (`honey`, `pumpkins`, `organic`, `expenses`, `meal_capacity`) for that town.
4. Verify that typing `market_list[["Norwich"]]$meal_capacity` returns the correct number of meals.

### Exercise 2: Simulated Survey Biomass Pipeline (Challenge 2 Prep)

Simulate a survey dataset of 15 forest quadrats using deterministic vectors:
```R
# Pre-defined survey measurements:
soil_ph_vals <- c(4.2, 5.8, 5.1, 7.2, 4.8, 6.9, 5.3, 4.5, 7.8, 5.4, 6.1, 4.9, 5.2, 6.7, 5.0)
tree_counts  <- c(16,  12,  18,   8,  20,   5,  14,  22,   7,  19,  11,  15,  13,   9,  17)
```

1. Construct a data frame named `survey_df` containing two columns: `"soil_ph"` and `"tree_count"`.
2. Add a column named `"biomass"` using the formula:
   $$\text{biomass} = \text{tree\_count} \times (2.0 + 0.5 \times \text{soil\_ph})$$
3. Subset `survey_df` into a new data frame named `specialist_df` containing only quadrats that meet both specialist acid-tolerant thresholds simultaneously:
   * `soil_ph < 5.5`
   * `tree_count >= 15`
4. Calculate the arithmetic mean of the `"biomass"` column across all specialist plots in `specialist_df` and store it in a single scalar variable named `specialist_mean_biomass`.
5. Write an expression that checks if `nrow(specialist_df) > 0`, and prints the scalar `specialist_mean_biomass`.

### Exercise 3: Disturbance Matrix Transformation (Challenge 3 Prep)

Consider an environmental impact matrix where rows represent 5 distinct ponds and columns represent 4 observation time points:
```R
impact_matrix <- matrix(
  data = c(0.12, 0.45, 0.78, 0.22, 0.90,
           0.15, 0.50, 0.82, 0.20, 0.95,
           0.10, 0.40, 0.75, 0.25, 0.88,
           0.30, 0.65, 0.92, 0.35, 0.99),
  nrow = 5, ncol = 4
)
rownames(impact_matrix) <- paste0("Pond_", 1:5)
colnames(impact_matrix) <- paste0("Time_", 1:4)
```

1. Calculate the mean impact score for each pond across all 4 time points using `rowMeans()`.
2. Identify which ponds have an overall mean impact score strictly greater than `0.60`.
3. Create a copy of `impact_matrix` named `disturbed_matrix`. In this new matrix, set all individual impact values that exceed `0.80` to `NA` (representing localized population collapse).
4. Count how many total missing values (`NA`) exist in `disturbed_matrix` using `is.na()` and `sum()`.