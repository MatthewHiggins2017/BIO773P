# **Practical 2 - Accessing and Manipulating Composite Data**

---

## Introduction

In Practical 1, we worked with atomic vectors, which store one-dimensional sequences of elements of the same data type. However, scientific datasets, laboratory surveys, and computational models require **composite data structures** capable of organizing multi-dimensional, multi-variable, or heterogeneous information:

1. **Matrices:** Two-dimensional grids of values of a single atomic class (e.g., spatial grids, time-series profiles).
2. **Lists:** Flexible, ordered collections where each element can be an entirely different type, length, or structure (e.g., grouping diverse survey metadata, sample vectors, and station parameters).
3. **Data Frames:** The standard tabular format for statistical analysis in R, where rows represent individual observations and columns represent distinct variables of varying types.

In this practical, you will learn to construct, inspect, slice,
modify, and aggregate each of these composite structures. Mastering
these techniques will enable you to assemble structured data
pipelines, validate multi-column datasets, and execute multi-condition
filtering.

---

## Matrices: Two-Dimensional Grids

A **matrix** is a two-dimensional rectangular array organized into rows and columns. Like an atomic vector, every cell in a matrix must contain the exact same data class (numeric, character, or logical).

### Creating Matrices

Matrices can be constructed using `matrix()` (please please copy and execute commands
in boxes for later use of results; also make sure you understand each
line of code; free free to play with the code to see what happens):

```R
# Creating a matrix from a numeric sequence:
# By default, R populates matrices column-by-column:
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

We can also bind vectors of equal length together using `rbind()` (row bind) and `cbind()` (column bind):
```R
transect_a <- c(14.2, 15.1, 13.9)
transect_b <- c(16.0, 15.8, 16.4)

# Bind as rows (2 rows, 3 columns):
row_bound <- rbind(transect_a, transect_b)

# Bind as columns (3 rows, 2 columns):
col_bound <- cbind(transect_a, transect_b)
```

### Inspecting Matrix Dimensions and Naming

To check the dimensions of a matrix:

* `dim(m)`: Returns a vector `c(nrow, ncol)`.
* `nrow(m)`: Total number of rows.
* `ncol(m)`: Total number of columns.

You can assign and query names for rows and columns:
```R
colnames(col_bound) <- c("Station_A", "Station_B")
rownames(col_bound) <- c("Depth_1m", "Depth_5m", "Depth_10m")
col_bound
```

### Matrix Subsetting and Slicing

Subsetting a matrix requires two indices inside square brackets: `[row, column]`. Remember the mnemonic: **R**ow fi**r**st, **C**olumn se**c**ond (**RC**).

* `mat[r, c]`: Element at row `r`, column `c`.
* `mat[r, ]`: **Entire row `r`** (leaving the column blank extracts all columns).
* `mat[, c]`: **Entire column `c`** (leaving the row blank extracts all rows).
* `mat[c(1, 3), c(2, 3)]`: Sub-grid spanning rows 1 & 3 and columns 2 & 3.

```R
# Using mat1 from above:
mat1[2, 3]        # 10
mat1[1:2, ]       # First two rows across all columns
mat1[, 2]         # Entire second column: 5 6 7 8
```

> **Note on Dimension Dropping:** When you extract a single row or single column (e.g., `mat1[, 2]`), R automatically drops the matrix dimensions and returns a simple 1D vector. If you wish to preserve the 2D matrix structure, supply `drop = FALSE`:
> ```R
> mat1[, 2, drop = FALSE]  # Returns a 4x1 matrix
> ```

### Matrix Arithmetic and Margins

Mathematical operations on matrices occur element-by-element:
```R
mat1 * 5
mat1 + mat2
```

To summarize rows or columns efficiently, use R's built-in margin
functions:

* `rowSums(m)`, `colSums(m)`: Sum totals across rows or columns.
* `rowMeans(m)`, `colMeans(m)`: Arithmetic averages across rows or columns.

```R
colMeans(mat1)  # 2.5 6.5 10.5
```

#### Q1. Consider the matrix:
```R
sample_grid <- matrix(data = seq(10, 180, by = 10), nrow = 6, ncol = 3)
```
Without executing the code first:

1. How many total elements are in `sample_grid`?
2. What value is located at `sample_grid[4, 2]`?
3. Write an expression to replace all values in `sample_grid` strictly greater than `100` with `100`.

---

## Lists: Heterogeneous and Hierarchical Data

Atomic vectors and matrices are restricted to holding elements of one atomic class. A **list**, however, is a generic vector that can contain elements of **any class, length, or structure**—including vectors of different lengths, matrices, data frames, or other lists.

Lists are widely used in R to bundle related parameters, package complex experimental outputs, or return grouped records.

### Creating Lists

Lists are constructed using `list()`. Giving each element a descriptive name makes downstream retrieval clear and robust:

```R
expedition_record <- list(
  vessel_name     = "R/V Discovery",
  cruise_id       = 104L,
  sensor_readings = c(12.4, 13.1, 12.8, 14.0),
  gps_active      = TRUE,
  salinity_psu    = c(34.1, 34.2, 34.0)
)
```

In the Environment tab of RStudio, click the blue expand arrow next to
`expedition_record` to inspect its hierarchical structure.

### Accessing List Elements: `[` vs `[[` vs `$`

Understanding the distinction between single brackets `[` and double brackets `[[` is important when working with lists:

* **Single Brackets `[ ]` (Sub-listing):**
  Extracts a *slice* of the list. **It always returns a list**, even if you request only a single element:
  ```R
  sub <- expedition_record["vessel_name"]
  class(sub)  # "list"
  ```
  *Analogy:* Think of a list as a train with cargo wagons. `expedition_record[1]` uncouples the 1st wagon and hands you a shorter train consisting of that single wagon.

* **Double Brackets `[[ ]]` (Element Extraction):**
  Extracts the *actual object stored inside* the designated position or name:
  ```R
  name_val <- expedition_record[["vessel_name"]]
  class(name_val)  # "character"
  ```
  *Analogy:* `expedition_record[[1]]` opens up the 1st wagon, unloads the cargo inside, and hands you the cargo directly.

* **Dollar Sign `$` (Named Extraction):**
  Convenient shorthand for `[[ "name" ]]`:
  ```R
  expedition_record$sensor_readings  # Returns numeric vector: 12.4 13.1 12.8 14.0
  ```

```R
# You can immediately apply vector operations to extracted elements:
mean(expedition_record$sensor_readings)  # 13.075
```

### Accessing the Names of List Elements

You can see and modify the names of list elements using the `names()`
command:

```R
# List the names of all elements:
record_names <- names(expedition_record)
record_names

# Modify a name:
names(expedition_record)[which(record_names == "sensor_readings")] <-
    "sensor_readings_mA"
```

### Modifying and Adding List Elements

You can update existing elements, add new entries, or delete elements by assignment:

```R
# Modify existing element:
expedition_record$gps_active <- FALSE

# Add a new element:
expedition_record$chief_scientist <- "Dr. Vance"

# Remove an element by assigning NULL:
expedition_record$cruise_id <- NULL
```

See how `expedition_record` changed in the Environment tab.

### Nested Lists

Because list elements can be any object, you can nest lists within lists. This hierarchical structure is standard for multi-site monitoring campaigns or complex parameter configurations:

```R
station_reports <- list(
  Station_Alpha = list(temp = 14.2, turbidity = 2.1, certified = TRUE,  depth = 45.0),
  Station_Beta  = list(temp = 11.5, turbidity = 5.8, certified = FALSE, depth = 85.0),
  Station_Gamma = list(temp = 16.8, turbidity = 1.4, certified = TRUE,  depth = 32.5)
)

# Extract information for a specific station:
station_reports[["Station_Alpha"]]$temp        # 14.2
station_reports$Station_Gamma$certified        # TRUE
```

#### Q2. Given `station_reports` above:
1. Write an expression to retrieve the turbidity at `Station_Beta`.
2. Add a new station named `"Station_Delta"` to `station_reports` containing temperature 13.4, turbidity 3.0, certified status `TRUE`, and depth 60.0.
3. Assuming that value of `station_name` is a character expression
   (e.g., `station_name <- "Station_Gamma"`), giving the name of a
   station, extract the report of the station with that name.

---

## Data Frames: Tabular Scientific Datasets

A **data frame** is a two-dimensional table designed specifically for
statistical data analysis. Formally, a data frame is a named list of
vectors of **identical length**, where:

* Each **column** represents a variable (and must be of a single atomic class).
* Each **row** represents an observation (and can span multiple classes across columns).

Unlike a matrix (which forces all cells into one atomic type), a data frame holds numeric, character, and logical columns side by side.

### Creating Data Frames

Construct data frames using `data.frame()`:

```R
estuary_df <- data.frame(
  station_id    = 101:106,
  salinity_psu  = c(28.4, 32.1, 29.5, 34.8, 31.0, 33.2),
  sampling_depth = c(2.5,  5.0,  3.0, 12.0,  8.5,  4.0),
  sediment_type  = c("Sand", "Mud", "Sand", "Gravel", "Mud", "Sand"),
  is_marine      = c(FALSE, TRUE, FALSE, TRUE, TRUE, TRUE),
  stringsAsFactors = FALSE
)

estuary_df
```

Inspect the structure and dimensions of your data frame:
```R
dim(estuary_df)      # 6 rows, 5 columns
nrow(estuary_df)     # 6
ncol(estuary_df)     # 5
names(estuary_df)    # Column names
str(estuary_df)      # Structure overview
head(estuary_df, 3)  # First 3 rows
```

### Accessing Columns and Cells

Columns can be extracted as vectors using `$`, `[[ ]]`, or `[, "column_name"]`:

```R
# Extract a column as an atomic vector:
estuary_df$salinity_psu
estuary_df[["salinity_psu"]]
estuary_df[, "salinity_psu"]

# Extract a specific cell: row 2, column 'salinity_psu':
estuary_df[2, "salinity_psu"]  # 32.1
```

### Adding and Computing New Columns

You can add new variables directly or compute them from existing columns using vectorized expressions:

```R
# Compute an estimated hydraulic pressure index:
# pressure = sampling_depth * (1.0 + 0.02 * salinity_psu)

estuary_df$pressure_idx <- estuary_df$sampling_depth * (1.0 + 0.02 * estuary_df$salinity_psu)

estuary_df
```

---

## Subsetting and Filtering Data Frames

Data analysis routinely requires filtering data frames to isolate rows meeting environmental or experimental criteria.

### 1. Filtering with Row Logical Indexing

The standard and most robust method to filter a data frame is passing a logical condition into the **row index position**: `df[condition, ]`.

```R
# Find stations where salinity is strictly greater than 30.0:
saline_stations <- estuary_df[estuary_df$salinity_psu > 30.0, ]
saline_stations

# Combine multiple conditions:
# Salinity > 30.0 AND sampling depth >= 5.0:
deep_saline_df <- estuary_df[estuary_df$salinity_psu > 30.0 & estuary_df$sampling_depth >= 5.0, ]
deep_saline_df
```

> **Crucial Syntax Reminder:** Do not forget the comma inside the brackets! 
> * `estuary_df[condition, ]` filters **rows** and keeps all columns.
> * `estuary_df[condition]` would attempt to subset columns by name or index and cause errors.

### 2. Filtering with `subset()`

R also provides the helper function `subset()`, which evaluates column names directly:

```R
deep_saline_sub <- subset(estuary_df, salinity_psu > 30.0 & sampling_depth >= 5.0)
```

While `subset()` is convenient for quick interactive exploration, bracket indexing `df[condition, ]` is preferred inside scripted functions and packages due to unambiguous variable evaluation rules.

---

## Aggregation and Boundary Cases

Consider a situation where, once a data frame is filtered, summary
statistics are calculated across specific columns, e.g.:

```R
# Calculate the mean pressure index of the deep saline stations:
mean_pressure <- mean(deep_saline_df$pressure_idx)
mean_pressure
```

### Boundary Case: Zero Qualifying Rows

In computational pipelines and simulations, it is common that **no observations satisfy** the filtering criterion. Your code must handle empty subsets predictably:

```R
# Filter for extreme criteria that do not occur in our data:
extreme_df <- estuary_df[estuary_df$salinity_psu > 45.0 & estuary_df$sampling_depth > 50.0, ]

nrow(extreme_df)  # Returns 0
```

If you calculate the mean of an empty numeric vector:
```R
mean(extreme_df$pressure_idx)  # Returns NaN (Not a Number)
```

To handle such situations in complicated scripts, you can check
whether `nrow(df) > 0`:

```R
# Checking if rows exist before computing summary:
has_rows <- nrow(extreme_df) > 0
has_rows  # FALSE
```

---

## Missing Data in Data Frames

Tabular datasets frequently contain missing values (`NA`). 

```R
field_records <- data.frame(
  plot_id     = 1:4,
  temperature = c(15.2, NA, 18.1, 14.7),
  dissolved_o2 = c(7.8, 8.1, NA, 6.9)
)
```

* `is.na(field_records)`: Returns a logical matrix indicating missing cells across the table.
* `complete.cases(field_records)`: Returns a logical vector of length `nrow`, which is `TRUE` only for rows that have **no missing values in any column**.
* `na.omit(field_records)`: Strips out all rows containing any `NA`.

```R
complete.cases(field_records)  # TRUE FALSE FALSE  TRUE

clean_records <- field_records[complete.cases(field_records), ]
clean_records
#   plot_id temperature dissolved_o2
# 1       1        15.2          7.8
# 4       4        14.7          6.9
```

#### Q3. In `estuary_df` from above:
1. Filter the data frame to select all rows where `sediment_type == "Sand"` and `pressure_idx > 4.0`.
2. Extract the `salinity_psu` column of these qualifying stations as a plain numeric vector.
3. Compute the arithmetic mean salinity of these qualifying stations.

---

## Practical Review Exercises

Work through these multi-step exercises in your R script. Ensure your code is thoroughly documented, variables are clearly named, and outputs are verified in the console.

### Exercise 1: Assembling and Disassembling Lists and Data Frames

Suppose you receive data though different channels informing your about oceanographic sampling buoys:
```R
buoy_codes    <- c("Bay_North", "Shoal_East", "Harbour_Deep", "Cove_West")
surface_temp  <- c(16.5, 14.2, 18.1, 15.0)
ph_readings   <- c(8.1, 7.8, 8.3, 7.9)
sensor_ok     <- c(TRUE, TRUE, FALSE, TRUE)
depth_meters  <- c(42.0, 18.5, 88.0, 25.0)
```

1. Combine these vectors into a single data frame named `buoy_df` with
   appropriate column names.
2. In water quality monitoring, each station's acid-neutralizing
   capacity index (ANC) is estimated as:
   ```
   ANC = depth_meters x (0.5 + 0.1 x ph_readings)
   ```
   Add this calculated column to `buoy_df`.
   
3. Construct a list from the third row of `buoy_df` by extrating the
   row and applying `as.list()`.
   
4. Construct an empty list named `error_report`. Then add the list
   generate in the previous output as an element to `error_report`,
   and name that element according to the corresponding buoy code.


### Exercise 2: Tabular Water Quality Pipeline

Create a simulated limnology survey table of 12 lake sampling stations using the following vectors:
```R
temp_data <- c(18.5, 21.2, 16.8, 22.5, 17.1, 23.0, 19.4, 15.9, 20.8, 24.1, 17.8, 18.2)
do_data   <- c(7.2,  5.1,  8.4,  4.8,  7.9,  4.2,  6.5,  8.8,  5.6,  3.9,  7.5,  6.8)
```

1. Construct a data frame named `lake_survey` containing columns `"temperature"` and `"dissolved_o2"`.
2. Add a calculated column named `"oxygen_sat"` based on the
   formulation:
   ```
   oxygen_sat = dissolved_o2 x (1.2 + 0.04 x temperature)
   ```
3. Subset `lake_survey` into a new data frame named `hypoxic_risk_df` containing only stations that meet both adverse environmental criteria simultaneously:
   * `temperature > 20.0`
   * `dissolved_o2 < 5.5`
4. Calculate the mean of `"oxygen_sat"` across all qualifying stations in `hypoxic_risk_df` and store it in a single scalar variable named `mean_risk_sat`.
5. Check whether `nrow(hypoxic_risk_df) > 0` and display this data frame.

### Exercise 3: Thermal Anomaly Matrix Transformation

Consider a temperature anomaly grid where rows represent 5 observation depths and columns represent 4 seasonal quarters:
```R
thermal_grid <- matrix(
  data = c(0.45, 0.82, 1.25, 0.35, 1.40,
           0.50, 0.90, 1.30, 0.40, 1.55,
           0.40, 0.78, 1.15, 0.30, 1.35,
           0.60, 1.05, 1.45, 0.55, 1.70),
  nrow = 5, ncol = 4
)
rownames(thermal_grid) <- paste0("Depth_", c(5, 10, 25, 50, 100), "m")
colnames(thermal_grid) <- paste0("Quarter_", 1:4)
```

1. Calculate the mean thermal anomaly across all 4 quarters for each depth using `rowMeans()`.
2. Identify which depths have an overall mean thermal anomaly strictly greater than 1.00.
3. Create a copy of `thermal_grid` named `censored_grid`. In this new matrix, set all individual anomaly values that exceed 1.20 to `NA` (representing sensor threshold exceedance).
4. Count how many total missing values (`NA`) exist in `censored_grid` using `is.na()` and `sum()`.
