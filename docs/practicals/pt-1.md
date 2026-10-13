# **Practical 1 - Introduction to RStudio, Basic Data Types, and Vectors**

---

## Introduction

This practical series teaches core computational programming in R,
focusing on the concepts required for computational modelling, data
manipulation, and scientific workflows.

The practicals move quickly. You will need to engage actively with the
concepts, experiment in your script editor, and complete the exercises
to build the technical foundation required for the assessment
challenges. Make full use of R's built-in help system, discuss
problems with your neighbour, and call over a demonstrator if you need
clarification.

---

## The RStudio Environment

Open RStudio on your computer.

RStudio is an **integrated development environment** (IDE)—a front-end
interface that makes working with R intuitive and productive. R itself
is the underlying engine doing all the actual work in the background.

Acquaint yourself with the four primary panels in RStudio:

```
+------------------------------------+------------------------------------+
|                                    |                                    |
|         Source / Script Editor     |          Environment / History     |
|          (Top Left)                |           (Top Right)              |
|                                    |                                    |
+------------------------------------+------------------------------------+
|                                    |                                    |
|         Console / Terminal         |          Files / Plots / Help      |
|          (Bottom Left)             |           (Bottom Right)           |
|                                    |                                    |
+------------------------------------+------------------------------------+
```

### The Console

* In the **bottom-left** panel, find the tab called **Console**. You can type commands here for immediate execution.
  Type `1 + 2` and press `Enter` (or `Return`). As output, you will see:
  ```R
  [1] 3
  ```
  The `[1]` indicates that the display line begins with the *1st* element of the returned vector (which here contains a single value). The `3` is the computed result.

* Continuing in the Console, type:
  ```R
  a <- 7 - 2
  ```
  and press `Enter`. The answer is not printed to the console. Look at the **Environment** tab in the **top-right** panel: it now lists `a` with its assigned value `5`.

* You can inspect the value of `a` by typing `a` into the console and pressing `Enter`. R evaluates the symbol and prints `[1] 5`.

* In the Console, you can cycle through previously entered commands using the `Up` and `Down` arrow keys on your keyboard.

> **Important:** While the Console is useful for quick calculations, scientific programming must be **reproducible**. Never rely on commands typed directly into the Console for your permanent work. All code, data pipelines, and calculations should be written into script files.

---

### The R Script Editor

We will write all permanent commands into an R script file and execute them from the editor.

* In the menu bar, click **File -> New File -> R Script** (or use `Ctrl+Shift+N` on Windows/Linux, `Cmd+Shift+N` on macOS). A blank editor tab will appear in the top-left panel.

* Type a mathematical expression:
  ```R
  2 * a + 8
  ```
  Press `Enter`. A new line opens, but nothing is calculated yet: we have not instructed R to evaluate the line.

* Place your cursor anywhere on the line with `2 * a + 8` and press `Ctrl+Enter` (Windows/Linux) or `Cmd+Enter` (macOS). The line is automatically sent to the Console below and evaluated.

* In your script, type:
  ```R
  a <- a + 1000
  ```
  Press `Ctrl+Enter` (`Cmd+Enter`). Observe how the value associated with `a` in the **Environment** tab updates immediately.

* You can run multiple lines or an entire script at once. Select
  several lines, or even just parts of lines, with your mouse (or press `Ctrl+A` / `Cmd+A` to highlight the entire script), then press `Ctrl+Enter` (`Cmd+Enter`).

* To save your script, click **File -> Save** (`Ctrl+S` / `Cmd+S`). Choose an informative filename. RStudio will automatically append the `.R` file extension.

---

### The Help System

The **Help** tab in the bottom-right panel is an essential resource.

* In your script editor or console, type `sum` and place the text cursor within the word. Press `F1`. The documentation for `sum` will open in the Help tab. Alternatively, run:
  ```R
  ?sum
  ```

* Help pages follow a consistent structure. When learning a new function, read the sections in this recommended order:
  1. **Description:** A concise summary of what the function accomplishes.
  2. **Usage & Arguments:** The parameters the function expects, their
     default values, and the expected input structure. Then skip down
     to...
  3. **Value:** What the function returns (a scalar number, a vector, a list, etc.).
  4. **Examples:** Working code snippets at the bottom of the page that you can paste into your script and run.

* If you do not know the exact name of a function, use a double question mark to search across all installed packages:
  ```R
  ??logarithm
  ```

#### Q1. Using the Help page for `sum`:
Find out how to compute the sum of all integers from 1 to 100 using R's sequence operator `:`.

---

## Good Coding Practice

Writing readable, well-structured code is just as vital as writing
code that runs. In professional computational science, code is read
far more often than it is written. Clean layouts reduce the likelyhood
of errors and ensure that collaborators and assessors can follow your
analytical logic.

### White Space, Indentation, and Comments

* **Comments:** Any text following a `#` character is ignored by R
  until the end of the line. Try to use comments to summarise *to what end* a calculation is performed, rather than merely restating the code.
* **White Space:** Spaces around operators (`+`, `-`, `*`, `<-`, `==`) and after commas make code legible. Compare `x<-3*y+2` with `x <- 3 * y + 2`.
* **Descriptive Names:** Avoid single-letter variables except for temporary indexing counters. A variable named `canopy_cover` conveys intent; a variable named `cc` or `x` invites errors.
* **Consistency:** Stick to snake case (`my_variable_name`) for variable and function names.

Consider the contrast between these two scripts performing identical calculations:

#### Bad Practice:
```R
boooom <-0.01
blah2          =0.005
    blah3<- 0.0036
bigblah                           = boooom+ blah2        + blah3
tadayeahmanfunky<-100*exp(bigblah*10)
tadayeahmanfunky
```

#### Good Practice:
```R
#-------------------------------------------------------------------------------
# Program:     bacterial_growth.R
# Purpose:     Calculates projected population size under exponential growth.
# Inputs:      Nutrient availability, temperature anomaly, relative humidity.
#-------------------------------------------------------------------------------

# Define environmental input parameters
nutrients   <- 0.01    # Proportional nutrient supplementation
temperature <- 0.005   # Degrees C deviation from 20 C baseline
humidity    <- 0.0036  # Proportional humidity index

# Define initial culture parameters
starting_cells <- 100  # Initial cell count
incubation_hrs <- 10   # Duration in hours

# Calculate compound growth rate
growth_rate <- nutrients + temperature + humidity

# Project final population size: N(t) = N_0 * exp(r * t)
final_cells <- starting_cells * exp(growth_rate * incubation_hrs)

# Display final cell count
final_cells
```

#### Q2. In your script editor, write a clean, well-commented script that calculates the volume and total surface area of a rectangular storage container:
* Length = 6.5m
* Width = 3.2m
* Height = 2.4m

Store each dimension in a descriptively named variable. Compute the
volume (L x W x H) and total surface area (2 x [LW + LH + WH]), save
them in separate variables, and print both results to the console.

---

## Variables and Assignment

A variable stores a value or an object in computer memory under a
designated name. In R, assignment is performed using the arrow
operator `<-`, typed as a less-than sign and a hyphen (please copy and
execute commands in boxes for later use of results; also make sure you
understand each line of code; feel free to play with the code to see
what happens):

```R
dissolved_oxygen_mg_l <- 8.45
sensor_depth_m        <- 12.0
```

Always use `<-` for assignment. Although R permits the equals sign `=`
for assignment, `=` is reserved by convention for setting function arguments inside function calls (e.g., `seq(from = 1, to = 10)`).

Rules for variable names in R:

* Names can contain letters, numbers, underscores (`_`), and periods (`.`). Using underscores (`snake_case`) is standard practice.
* Names must start with a letter (or a period not immediately followed by a number).
* Names cannot contain spaces or arithmetic operators (`-`, `+`, `*`, `/`).
* R is strictly **case-sensitive**: `Water_Temp`, `water_temp`, and `WATER_TEMP` refer to three completely different variables.

---

## Data Classes and Types

Every piece of data stored in R belongs to an underlying class that dictates how R executes operations on it. The core atomic data types are:

1. **Numeric (Double):** Real numbers with decimal precision (e.g., `3.1415`, `-0.5`, `42`).
2. **Integer:** Whole numbers (e.g., `5L`, `12L`). In R, numbers entered without a decimal point are still stored as doubles by default unless explicitly followed by `L` or converted via `as.integer()`.
3. **Character:** Text strings enclosed in matching single or double quotation marks (e.g., `'Salmo trutta'`, `"Transect_A"`).
4. **Logical:** Boolean truth values: `TRUE` or `FALSE` (unquoted, uppercase).

You can check the class of any variable using `class()`. Set

```R
station_id      <- "Station_North_4"
water_salinity  <- 34.8
replicate_count <- 4L
sensor_active   <- TRUE
```

Now try


```R
class(station_id)       
class(water_salinity)   
class(replicate_count)  
class(sensor_active)    
```

### Type Checking and Explicit Conversion

In computational pipelines and data ingestion scripts, code must often verify that inputs conform to expected types. R provides a family of `is.*()` functions to verify types, and `as.*()` functions to coerce between types:

| Type Check | Conversion Function | Example Behaviour |
| :--- | :--- | :--- |
| `is.numeric(x)` | `as.numeric(x)` | `"14.75"` -> `14.75` |
| `is.integer(x)` | `as.integer(x)` | `14.9` -> `14L` (truncates decimal) |
| `is.character(x)` | `as.character(x)` | `250` -> `"250"` |
| `is.logical(x)` | `as.logical(x)` | `1` -> `TRUE`, `0` -> `FALSE` |

```R
raw_reading <- "32.18"
is.numeric(raw_reading)      # FALSE (stored as character string)

clean_reading <- as.numeric(raw_reading)
is.numeric(clean_reading)    # TRUE
```

If you attempt to convert an incompatible character string to a number, R returns `NA` (Not Available) and issues a warning:
```R
as.numeric("invalid_sensor_signal")  # Returns NA with a warning
```

#### Q3. What is the value and class of `result` after evaluating each of the following expressions? Predict the output before running the code in R:
* `result <- as.integer(15.82)`
* `result <- as.logical(0)`
* `result <- as.character(FALSE)`
* `result <- as.numeric("18.4") <= 20`

---

## Logical Operators and Expressions

A logical expression evaluates a condition and resolves to either
`TRUE` or `FALSE`. Logical expressions are used, for example, for
filtering data, testing for unusual conditions conditions ("edge
cases"), and verifying input integrity.

### Relational Comparison Operators

* `>` : Greater than
* `<` : Less than
* `>=` : Greater than or equal to
* `<=` : Less than or equal to
* `==` : Exactly equal to (two equals signs; single `=` is assignment!)
* `!=` : Not equal to

```R
turbidity_ntu <- 4.8

turbidity_ntu < 5.0   # TRUE
turbidity_ntu == 5.0  # FALSE
turbidity_ntu != 0.0  # TRUE
```

### Boolean Combination Operators

Individual logical tests can be combined using Boolean logic:

* `&` : **AND** (evaluates to `TRUE` only if **both** conditions are `TRUE`).
* `|` : **OR** (evaluates to `TRUE` if **at least one** condition is `TRUE`).
* `!` : **NOT** (inverts `TRUE` to `FALSE`, and `FALSE` to `TRUE`).

```R
## Example use of logical variables
# Set paramters
dissolved_o2 <- 6.2
water_temp   <- 14.5

# Both conditions must hold:
hypoxia_risk <- (dissolved_o2 < 4.0) & (water_temp > 20.0)
hypoxia_risk  # FALSE

# At least one condition must hold:
suboptimal <- (dissolved_o2 < 5.0) | (water_temp > 18.0)
suboptimal    # FALSE

# Negation:
is_optimal <- !suboptimal
is_optimal    # TRUE
```

#### Truth Table Summary:
* `TRUE & TRUE` -> `TRUE`
* `TRUE & FALSE` -> `FALSE`
* `FALSE & FALSE` -> `FALSE`
* `TRUE | FALSE` -> `TRUE`
* `FALSE | FALSE` -> `FALSE`
* `!TRUE` -> `FALSE`

#### Q4. Given `a <- 18` and `b <- 35`, write logical expressions in R that test:
1. Whether `a` is strictly between 15 and 25 (exclusive of endpoints).
2. Whether `a` is less than 10 **or** `b` is greater than or equal to 30.
3. Whether `b` is divisible by 5 without a remainder (hint: use the modulo operator `%%`, where `b %% 5 == 0` evaluates divisibility).

---

## Vectors: The Fundamental 1D Structure

A **vector** is an ordered collection of elements that **must all belong to the same atomic class**. If you attempt to combine items of different classes into a single vector, R silently converts them to the most flexible type (character is most flexible, followed by numeric, followed by logical).

Oddly, in R, an individual scalar such as `x <- 5` is simply a vector of length 1.

### Creating Vectors

Construct vectors using `c()` (combine), sequence generators, or repeat functions:

```R
# Direct combination:
conductivity_vals <- c(450, 520, 480, 610, 590)
site_labels       <- c("Estuary_1", "Estuary_2", "Estuary_3")
equipment_passed  <- c(TRUE, TRUE, FALSE, TRUE)

# Sequences:
depth_profile <- seq(from = 0.0, to = 25.0, by = 5.0)  # 0, 5, 10, 15, 20, 25
station_ids   <- 101:108                               # Whole numbers 101 to 108
time_steps    <- seq(from = 0, to = 1, length.out = 5) # 5 evenly spaced values

# Repetitions:
baseline_zeros <- rep(0, times = 8)
treatment_tags <- rep(c("Ambient", "Enriched"), times = 3)
block_labels   <- rep(c("Zone_A", "Zone_B"), each = 4)
```

### Inspecting Vector Properties

* `length(x)`: Total number of elements.
* `class(x)`: The atomic class of the elements.
* `head(x, n)`: First $n$ elements (defaults to 6).
* `tail(x, n)`: Last $n$ elements.

```R
length(conductivity_vals)  # 5
class(conductivity_vals)   # "numeric"
```

#### Q5. Generate a vector named `temperature_steps` that starts at 12.0, ends at 24.0, and contains exactly 25 evenly spaced values. What is the value of its 9th element?

---

## Vectorized Arithmetic and Recycling

In R, mathematical operations applied to vectors execute **element-by-element** without requiring loops. This vectorization makes operations computationally efficient and concise.

### Scalar-Vector Arithmetic

When an operation combines a vector with a single scalar, the scalar operation is applied to each element individually:

```R
nitrate_ppm <- c(1.2, 2.5, 3.1, 0.8)

# Convert all measurements by adding a calibration offset of 0.3:
nitrate_ppm + 0.3          # 1.5 2.8 3.4 1.1

# Multiply each measurement by a conversion factor:
nitrate_ppm * 1000         # 1200 2500 3100  800

# Compute estimated ionic flux: flux = 1.5 * concentration + 0.2
nitrate_ppm * 1.5 + 0.2    # 2.00 3.95 4.85 1.40
```

### Vector-Vector Arithmetic and the Recycling Rule

When combining two vectors of identical length, arithmetic occurs pairwise:

```R
flow_rates <- c(2.0, 3.5, 1.8)
widths     <- c(10.0, 12.0, 8.0)

# Pairwise product:
discharge <- flow_rates * widths
discharge  # 20.0 42.0 14.4
```

If two vectors differ in length, R **recycles** (repeats) the elements of the shorter vector until it matches the length of the longer vector:

```R
sample_depths <- c(10, 20, 30, 40, 50, 60)
adjustments   <- c(1, 2)

sample_depths + adjustments
# adjustments is recycled to c(1, 2, 1, 2, 1, 2)
# Result: 11 22 31 42 51 62
```

> **Warning:** If the length of the longer vector is not an exact integer multiple of the length of the shorter vector, R still executes the operation using partial recycling, but issues a warning message. In scientific scripts, this often indicates an accidental indexing or alignment mismatch.

#### Q6. What does the following expression return?
```R
c(2, 4, 6, 8, 10, 12) * c(1, -1)
```
Explain the recycling process that produced this output.

---

## Vector Subsetting and Indexing

Extracting and modifying specific subsets of a vector is a very common
operation in R. We use square brackets `[]` for indexing.

There are four primary indexing mechanisms:

### 1. Positive Integer Indexing (Extraction)

Specify the 1-based index positions of the elements you wish to retrieve:

```R
salinity <- c(31.2, 32.5, 34.1, 33.8, 30.9, 35.4)

salinity[1]          # First element: 31.2
salinity[4]          # Fourth element: 33.8
salinity[c(1, 3, 5)] # First, third, and fifth: 31.2 34.1 30.9
salinity[2:4]        # Elements 2 through 4: 32.5 34.1 33.8
```

### 2. Negative Integer Indexing (Exclusion)

Negative integers instruct R to omit the designated positions:

```R
salinity[-1]         # Drops the first element
salinity[-c(2, 5)]   # Drops the 2nd and 5th elements
```

### 3. Logical Indexing (Filtering by Condition)

You can supply a logical vector of the same length inside the brackets. R retains only elements where the corresponding index entry is `TRUE`:

```R
measurements <- c(12.5, 8.1, 19.4, 6.2, 14.8)
keep_mask    <- c(TRUE, FALSE, TRUE, FALSE, TRUE)
measurements[keep_mask]  # 12.5 19.4 14.8
```

Because relational comparisons generate logical vectors, conditions can be placed directly inside the brackets:

```R
# Extract all salinity readings strictly greater than 33.0:
high_salinity <- salinity[salinity > 33.0]
high_salinity  # 34.1 33.8 35.4

# Extract salinity readings between 31.5 and 34.0:
mid_salinity <- salinity[salinity >= 31.5 & salinity <= 34.0]
mid_salinity   # 32.5 33.8
```

### 4. Named Indexing

Elements of a vector can have names assigned to them. You can extract elements using character names:

```R
station_depths <- c(Stn_A = 14.5, Stn_B = 28.2, Stn_C = 8.7)
station_depths["Stn_B"]                # Returns 28.2
station_depths[c("Stn_A", "Stn_C")]   # Returns 14.5 8.7
```

---

## Modifying Vectors and In-Place Replacement

Indexing allows you to update or correct specific entries in place using assignment:

```R
sensor_readings <- c(14.2, -999.0, 15.8, 16.1, -999.0, 14.9)

# Correct a single entry at index 2:
sensor_readings[2] <- 14.5

# Replace all error codes (-999.0) with NA:
sensor_readings[sensor_readings == -999.0] <- NA
sensor_readings
```

### Bounding and Clamping Values

Physical sensors or ecological rates often have theoretical operating boundaries. Values falling outside those boundaries can be clamped to valid limits using logical indexing:

```R
photometer_absorbance <- c(-0.05, 0.42, 1.85, 2.15, 0.90, -0.01)

# Clamp lower boundary at 0.0 (no negative absorbance):
photometer_absorbance[photometer_absorbance < 0.0] <- 0.0

# Clamp upper boundary at 2.0 (detector saturation limit):
photometer_absorbance[photometer_absorbance > 2.0] <- 2.0

photometer_absorbance  # 0.00 0.42 1.85 2.00 0.90 0.00
```

#### Q7. Given the a vector of this structure:
```R
dissolved_co2 <- c(18.2, 24.5, 9.8, 31.0, 42.1, 14.3, 5.4, 28.7)
```
Write code to:

1. Extract all concentrations that are strictly less than 12.0 or strictly greater than 30.0.
2. Clamp the vector so that any value below 10.0 is set to 10.0, and any value exceeding 35.0 is set to 35.0.

---

## Missing Data (`NA`) and Vector Summaries

In real-world field sampling and laboratory assays, measurements can be missing due to equipment downtime or uncollected samples. In R, missing entries are represented by the special constant `NA` (Not Available).

### Behaviour of `NA`

`NA` represents an unknown quantity. Standard arithmetic and relational comparisons involving `NA` yield `NA`:

```R
10 + NA       # NA
NA > 5        # NA
NA == NA      # NA (you cannot test whether an unknown equals an unknown)
```

To test whether entries are missing, **never use `x == NA`**. Always use the dedicated function `is.na()`:

```R
chlorophyll_a <- c(2.4, NA, 3.8, 1.9, NA, 4.5)

is.na(chlorophyll_a)
# FALSE  TRUE FALSE FALSE  TRUE FALSE

# Count missing entries:
sum(is.na(chlorophyll_a))  # 2

# Extract only valid (non-missing) measurements:
valid_chlorophyll <- chlorophyll_a[!is.na(chlorophyll_a)]
valid_chlorophyll  # 2.4 3.8 1.9 4.5
```

### Vector Summaries and `na.rm`

Common aggregation functions across vectors include:

* `sum()`: Sum of elements.
* `mean()`: Arithmetic mean.
* `min()`, `max()`: Extremes.
* `length()`: Total length (including any `NA` entries).

If a vector contains missing data, summary functions return `NA` by default to warn you of incomplete data. To compute the summary over all available valid observations, supply the argument `na.rm = TRUE`:

```R
mean(chlorophyll_a)                # NA
mean(chlorophyll_a, na.rm = TRUE)  # 3.15
```

### Logical Reductions: `all()`, `any()`, and `which()`

When checking input validity or data criteria across vectors:

* `any(condition)`: Returns `TRUE` if at least one element satisfies the condition.
* `all(condition)`: Returns `TRUE` only if every element satisfies the condition.
* `which(condition)`: Returns the *integer indices* where the condition evaluates to `TRUE`.

```R
pressures_kpa <- c(101.3, 102.5, 98.4, 104.1, 99.2)

any(pressures_kpa < 100.0)    # TRUE
all(pressures_kpa > 90.0)     # TRUE
which(pressures_kpa > 102.0)  # 2 4 (2nd and 4th entries)
```

#### Q8. You are given the following record of stream water temperatures:
```R
stream_temps <- c(14.2, 16.5, NA, 15.1, 19.8, NA, 13.9, 17.0)
```
Write R expressions to:

1. Check whether any recorded temperature exceeds 18.0C.
2. Check whether all non-missing measurements are strictly above 12.0C.
3. Compute the arithmetic mean of all non-missing temperatures.
4. Replace all `NA` values in `stream_temps` with the baseline value 15.0C.

---

## Practical Review Exercises

Work through these multi-step exercises in your R script. Ensure your code is formatted cleanly, variables are descriptively named, and outputs are verified in the console.

### Exercise 1: Input Validation Logic

Suppose an analytical function receives five parallel vectors containing sensor deployment data:
```R
buoy_ids     <- c("Buoy_01", "Buoy_02", "Buoy_03", "Buoy_04", "Buoy_05")
sensor_depth <- c(5.0, 10.0, 15.0, 20.0, 25.0)
salinity_psu <- c(34.2, 35.1, 33.8, 34.9, 35.5)
battery_pct  <- c(98.0, 85.5, 92.0, 78.0, 88.5)
is_active    <- c(TRUE, TRUE, TRUE, FALSE, TRUE)
```

Write logical checking expressions that test (without using `if`
statements or loops):

1. Whether all five vectors have the exact same length. (Tip: all
vectors have the same length if each has the same length as
`buoy_ids`.)
2. Whether all values in `sensor_depth`, `salinity_psu`, and
   `battery_pct` are strictly positive (> 0).
3. Whether `battery_pct` does not exceed 100.0 for any buoy.
4. Combine these three tests into a single logical expression that evaluates to `TRUE` if and only if **all** validation criteria are met.

### Exercise 2: Vectorized Environmental Flux Calculation

A marine survey records water temperature and dissolved oxygen across 8 depth stations:
```R
temp_celsius    <- c(18.2, 17.5, 16.1, 14.8, 13.2, 12.0, 11.5, 10.8)
oxygen_mg_l     <- c(8.9,  8.5,  7.8,  7.2,  6.4,  5.8,  5.2,  4.9)
```

1. The apparent metabolic oxygen consumption index (OCI) at each depth is estimated as:
   OCI = oxygen_mg_l x (1.5 + 0.08 x temp\_celsius).
   Compute this for all stations and store the result in a vector named `station_oci`.
2. Construct a logical vector `hypoxic_risk` identifying depths where `temp_celsius < 15.0` **and** `oxygen_mg_l < 6.0`.
3. Using `hypoxic_risk` as an index, extract the OCI values for the qualifying depths.
4. Calculate the average OCI across these qualifying depths.

### Exercise 3: Sensor Outlier Masking

A coastal wave-monitoring buoy records wave height anomalies across 8 measurement cycles:
```R
wave_anomalies <- c(0.42, 1.85, NA, 0.78, 2.40, NA, 1.15, 0.95)
```
During stormy conditions, sensors can experience severe wave spray. Any reading from an active sensor that strictly exceeds a critical wave anomaly of 1.50 is considered corrupted and must be flagged. Sensors that were offline (`NA`) must remain flagged as `FALSE` (not corrupted).

1. Write a single vectorized expression that creates a logical vector `corrupted` of length 8, where an element is `TRUE` if and only if the reading is non-missing **and** strictly greater than 1.50. Ensure `corrupted` contains **only `TRUE` and `FALSE` values, with no `NA` entries**.
2. Using `corrupted` as an index, replace the corrupted readings in `wave_anomalies` with `NA`.
3. Verify that uncorrupted valid measurements and previously missing readings remained in their proper states.
