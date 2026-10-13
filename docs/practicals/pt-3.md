# **Practical 3 - Functions, Flow Control, and Defensive Programming**

## Introduction

In Practicals 1 and 2, you learned how to manipulate atomic vectors
and composite structures (matrices, lists, and data frames) using
vectorized expressions. In this practical, we shift from executing
one-off commands to constructing **reusable code**.

Modern scientific workflows require code that is modular, transparent,
and robust against unexpected inputs. You will learn to:

1. **Encapsulate logic into custom functions** with formal arguments, default parameters, and controlled return values.
2. **Implement defensive programming** by validating input types, checking dimensions, and halting execution with informative error messages (`stop()`).
3. **Control execution flow** using scalar conditional branching (`if` / `else if` / `else`).
4. **Automate repetitive computations** using pre-allocated loops (`for` / `while`), including iteration skips (`next`) and loop termination (`break`).
5. **Combine function definitions** to for more difficult tasks.

We will focus on breaking down multi-step scientific problems into
clear, verifiable programmatic statements. 

---

## The Anatomy of an R Function

A function is an encapsulated, named block of code that accepts inputs
(arguments), executes a set of instructions, and returns an output
object.

Functions provide four decisive advantages in scientific programming:

* **Elimination of Duplication:** You write and debug a calculation once, rather than copying and pasting lines across your script.
* **Isolation (Scope):** Variables created inside a function exist only during its execution. They do not pollute or overwrite variables in your global workspace.
* **Testability:** You can systematically test a function against edge
  cases to ensure its computational
  correctness.
* **Encapsulation:** Once you (or somebody else) has written a working
  R function, you (or others) only need to understand intuitively _what_
  it does rather than considering the details of _how_ it is (best) done.

### Basic Syntax and Return Values

Functions are created using the `function` keyword and assigned to a variable name using `<-`:

```R
# Define a function to calculate acoustic sound velocity in seawater (Mackenzie, 1981):
calc_sound_speed <- function(temp_c, salinity_psu, depth_m) {
  speed <- 1448.96 + 4.591 * temp_c - 0.05304 * (temp_c^2) + 
           1.340 * (salinity_psu - 35.0) + 0.0163 * depth_m
  return(speed)
}
```

To execute (call) the function, pass the arguments by position or by name:

```R
# By position:
calc_sound_speed(12.5, 34.2, 250.0)

# By name (preferred for clarity and order independence):
calc_sound_speed(depth_m = 250.0, temp_c = 12.5, salinity_psu = 34.2)
```

> **Rules of Return:**

> 1. In R, `return(value)` explicitly terminates the function and passes `value` back to the caller.
> 2. If `return()` is omitted, R automatically returns the **last evaluated expression** in the function body. However, in scientific programming, writing explicit `return()` statements makes your exit points unambiguous.
> 3. A function can return only **one single object**. If you need to return multiple pieces of information (e.g., a numeric summary, a filtered data frame, and a status flag), package them into a named `list`.

### Default Arguments

You can provide fallback default values for arguments in the function header:

```R
calc_hydrostatic_pressure <- function(depth_m, gravity = 9.80665, water_density = 1025.0) {
  # pressure in kiloPascals (kPa)
  pressure_kpa <- (water_density * gravity * depth_m) / 1000.0
  return(pressure_kpa)
}

# Uses defaults for gravity and water density:
calc_hydrostatic_pressure(depth_m = 100.0)

# Overrides water density for freshwater lake:
calc_hydrostatic_pressure(depth_m = 100.0, water_density = 1000.0)
```

### Variable Scoping: Local vs Global

Consider this scenario:

```R
calibration_offset <- 1.5

adjust_reading <- function(reading) {
  adjusted <- reading + calibration_offset
  return(adjusted)
}

adjust_reading(10.0)  # Returns 11.5
```

The function was able to find `calibration_offset` in the global environment even though it was not passed as an argument. **This is dangerous practice.** If `calibration_offset` is renamed, modified, or cleared from memory later in your session, your function will break or return incorrect numbers without warning.

**Golden Rule of Modularity:** A function should be a self-contained "black box". It should rely **only** on arguments explicitly passed into it and variables defined locally within its body.

#### Q1. Consider the function:
```R
estimate_heat_content <- function(temp_c, water_mass_kg, specific_heat = 4184) {
  joules <- water_mass_kg * specific_heat * temp_c
  return(joules)
}
```

1. What value is returned by `estimate_heat_content(20.0, 50.0)`?
2. If you evaluate `joules` in your console after running this function, what happens? Why?

---

## Defensive Programming and Input Validation

When writing scripts for your own use, you might know what inputs are
expected. However, in production pipelines, shared repositories, and
testing during assessments, functions frequently receive incorrect, incomplete, or corrupted inputs.

**Defensive programming** means designing your function to inspect its
arguments before performing calculations, verifying that all
preconditions hold, and immediately halting with an informative error
message if an inconsistency is detected.

### Halting Execution with `stop()`

The `stop()` function interrupts execution immediately, aborts any ongoing calculations, and prints an error message to the console:

```R
validate_temperature <- function(temp_c) {
  if (temp_c < -273.15) {
    stop("Input temperature cannot be below absolute zero (-273.15 C).")
  }
  return(temp_c + 273.15)
}

validate_temperature(-300.0)
# Error in validate_temperature(-300): Input temperature cannot be below absolute zero (-273.15 C).
```

### Essential Validation Checks

Every robust scientific function should validate its inputs against four structural criteria:

1. **Dimensionality / Length:** Is the input a single scalar, or a vector of expected length?
   ```R
   if (length(x) != 1) {
     stop("Argument 'x' must be a scalar (vector of length 1).")
   }
   ```
2. **Data Type / Class:** Does the input conform to the required atomic class?
   ```R
   if (!is.numeric(x)) {
     stop("Argument 'x' must be numeric.")
   }
   ```
3. **Missingness:** Does the input contain forbidden `NA` or `NaN` values?
   ```R
   if (is.na(x)) {
     stop("Argument 'x' cannot be NA.")
   }
   ```
4. **Physical / Logical Bounds:** Do the values fall within valid operational limits?
   ```R
   if (x <= 0) {
     stop("Argument 'x' must be strictly positive.")
   }
   ```

### Combining Validation Checks

Let us build a validated function that computes dissolved oxygen saturation concentration:

```R
calc_do_saturation <- function(temp_c, salinity_psu = 35.0) {
  # Check types
  if (!is.numeric(temp_c) || !is.numeric(salinity_psu)) {
    stop("All arguments must be numeric.")
  }
  
  # Check scalar lengths
  if (length(temp_c) != 1 || length(salinity_psu) != 1) {
    stop("Inputs must be single scalar values.")
  }
  
  # Check for NA
  if (is.na(temp_c) || is.na(salinity_psu)) {
    stop("Inputs cannot be NA.")
  }
  
  # Check domain bounds
  if (temp_c < -2.0 || temp_c > 45.0) {
    stop("Temperature out of realistic environmental range (-2.0 C to 45.0 C).")
  }
  if (salinity_psu < 0.0 || salinity_psu > 50.0) {
    stop("Salinity must be between 0.0 and 50.0 PSU.")
  }
  
  # Calculation proceeds safely:
  sat_conc <- 14.6 - 0.4 * temp_c + 0.008 * (temp_c^2) - 0.05 * salinity_psu
  return(sat_conc)
}
```

Alternatively, this also works:
```R
calc_do_saturation <- function(temp_c, salinity_psu = 35.0) {
  # Check temp_c
  if (!is.numeric(temp_c) ||
      length(temp_c) != 1 ||
      is.na(temp_c) ){
    stop("Argument temp_c must be single numeric value.")
  }
  
  # Check salinity_psu
  if (!is.numeric(salinity_psu) ||
      length(salinity_psu) != 1 ||
      is.na(salinity_psu) ){
    stop("Argument salinity_psu must be single numeric value.")
  }
  
  # Check domain bounds
  if (temp_c < -2.0 || temp_c > 45.0) {
    stop("Temperature out of realistic environmental range (-2.0 C to 45.0 C).")
  }
  if (salinity_psu < 0.0 || salinity_psu > 50.0) {
    stop("Salinity must be between 0.0 and 50.0 PSU.")
  }
  
  # Calculation proceeds safely:
  sat_conc <- 14.6 - 0.4 * temp_c + 0.008 * (temp_c^2) - 0.05 * salinity_psu
  return(sat_conc)
}
```


> **Note on `||` vs `|` in Conditions:** When testing conditions inside an `if` statement, R provides double operators `&&` (short-circuit AND) and `||` (short-circuit OR). Unlike `&` and `|` (which evaluate across entire vectors element-by-element), `&&` and `||` evaluate **only the first element** and stop evaluating further tests as soon as the outcome is decided. Use `&&` and `||` inside scalar `if` statements.

#### Q2. Inspect the following function:
```R
calc_dilution_factor <- function(stock_conc, target_conc) {
  if (target_conc >= stock_conc) {
    stop("Target concentration must be strictly less than stock concentration.")
  }
  return(stock_conc / target_conc)
}
```
What happens if someone evaluates `calc_dilution_factor(stock_conc =
"100", target_conc = 10)`? What additional defensive check is required
to prevent this cryptic error message?

---

## Flow Control: Conditional Branching

Conditional branching allows your code to make decisions, executing different blocks of instructions depending on whether a given condition evaluates to `TRUE` or `FALSE`.

### Syntax of `if`, `else if`, and `else`

```R
classify_water_layer <- function(depth_m) {
  if (depth_m < 0) {
    stop("Depth cannot be negative.")
  }
  
  if (depth_m <= 200.0) {
    zone <- "Epipelagic"
  } else if (depth_m <= 1000.0) {
    zone <- "Mesopelagic"
  } else if (depth_m <= 4000.0) {
    zone <- "Bathypelagic"
  } else {
    zone <- "Abyssopelagic"
  }
  
  return(zone)
}
```

The execution runs sequentially:

1. R evaluates the condition in the initial `if`. If `TRUE`, it executes that code block and skips the rest of the chain.
2. If `FALSE`, it moves to the first `else if`. It tests each subsequent condition until one evaluates to `TRUE`.
3. If none of the conditions evaluate to `TRUE`, the fallback `else` block executes.

### Resolving Missing States in Comparisons

A frequent challenge in environmental modeling occurs when an observation might be an established numeric value **or** missing (`NA`).

Because comparing an unknown value to a number yields `NA` (e.g., `NA > 5` evaluates to `NA`), passing `NA` into a standard `if` statement will trigger an error: `Error in if (x > 5) : missing value where TRUE/FALSE needed`.

You must explicitly test for missingness using `is.na()` **before** performing numeric comparisons:

```R
# Model the establishment of a colonising species arriving at a survey site:
resolve_site_occupancy <- function(colonist_density, resident_density) {
  # Scenario A: The habitat patch is completely unoccupied
  if (is.na(resident_density)) {
    final_density <- colonist_density
  # Scenario B: The habitat patch is already occupied
  } else if (colonist_density > resident_density) {
    final_density <- colonist_density  # Colonist outcompetes resident
  } else {
    final_density <- resident_density  # Resident resists invasion
  }
  
  return(final_density)
}

resolve_site_occupancy(colonist_density = 15.2, resident_density = NA)    # 15.2
resolve_site_occupancy(colonist_density = 12.0, resident_density = 18.5) # 18.5
resolve_site_occupancy(colonist_density = 22.4, resident_density = 18.5) # 22.4
```

#### Q3. Write a function named `clamp_reading` that takes three arguments: `val`, `lower`, and `upper`.
1. The function should verify that `lower < upper`. If not, halt with `stop()`.
2. If `val` is strictly below `lower`, return `lower`.
3. If `val` is strictly above `upper`, return `upper`.
4. Otherwise, return `val`.

---

## Flow Control: Iteration with Loops

While R favors vectorized operations for simple mathematical
transformations, **iterative algorithms**, **step-by-step
simulations**, and **complex data manipulation** require explicit control loops.

### The `for` Loop

A `for` loop executes a block of code once for each element in a sequence:

```R
station_depths <- c(15.0, 45.0, 120.0, 310.0)

for (d in station_depths) {
  cat("Processing station at depth:", d, "meters\n")
}
```

### Index-Based Loops and the Vector Pre-Allocation

In scientific programming, you routinely use loops to populate a vector or table with calculated outputs.

#### The Anti-Pattern: Dynamic Growth (Do NOT Do This)
```R
# INEFFICIENT AND SLOW:
results <- c()  # Empty vector
for (i in 1:1000) {
  results <- c(results, i * 2)  # Re-allocates memory on every iteration!
}
```
Every time you call `c(results, ...)`, R has to allocate a completely new chunk of memory in your computer's RAM, copy the entire existing vector into it, append the new value, and delete the old chunk. For large loops, this causes catastrophic slowdowns.

#### The Correct Pattern: Vector Pre-Allocation
Always allocate the complete destination vector with its known final length **before** entering the loop, and populate values directly by index:

```R
n_steps <- 5
# Pre-allocate numeric vector of length 5 filled with NA:
simulated_profile <- rep(NA, n_steps)
# Alternatively: vector(mode = "numeric", length = n_steps)

for (i in seq_len(n_steps)) {
  simulated_profile[i] <- 10.0 + 2.5 * (i^1.5)
}

simulated_profile
```

> **Safe Indexing with `seq_len()` and `seq_along()`:** Never write `for (i in 1:length(vec))`. If `vec` happens to be empty (length 0), R evaluates `1:0`, creating a sequence `c(1, 0)` that runs two buggy iterations!
> Instead, always use:

> * `seq_len(n)`: Generates `1:n`, or an empty sequence if `n == 0`.
> * `seq_along(vec)`: Generates indices matching the exact elements of `vec`, handling empty vectors safely.

### Controlling Loop Execution: `next` and `break`

* `next`: Immediately skips the remainder of _the current loop iteration_ and advances to the next step.
* `break`: Immediately terminates _the entire loop_, jumping out to the code following the loop block.

```R
measured_temperatures <- c(14.2, -999.0, 16.8, 15.5, -999.0, 18.2)
calibrated_temps <- vector(mode = "numeric", length = length(measured_temperatures))

for (i in seq_along(measured_temperatures)) {
  current_reading <- measured_temperatures[i]
  
  # Skip corrupted sensor codes (-999.0):
  if (current_reading == -999.0) {
    calibrated_temps[i] <- NA
    next
  }
  
  # Process valid readings:
  calibrated_temps[i] <- current_reading * 1.02 + 0.1
}

calibrated_temps
# 14.584     NA 17.236 15.910     NA 18.664
```

#### Q4. Write a `for` loop that iterates over the integers from 1 to 20. 
Using `next`, configure the loop so that it prints only the numbers that are **multiples of 3** (hint: use the modulo operator `%%`, where `i %% 3 == 0` identifies multiples of 3).

---

## Practical Review Exercises

Work through these multi-step exercises in your R script. Focus on
translating ecological descriptions into clear, defensive functions
and structured, comprehensible code.

---

### Exercise 1: Multi-Site Invasiveness & Reserve Threat Scoring Pipeline

Conservation managers evaluate habitat patches for invasive weed colonization risk. The vulnerability of a patch depends on canopy openness, visitor footfall, and distance to the nearest road corridor.

You are provided with records from 6 protected nature reserves:

```R
reserve_names   <- c("Glen_Caladh", "Coed_Cariad", "Cairn_Moss", "Firth_Fen", "Dune_Reach", "Alder_Meadow")
canopy_cover    <- c(0.85, 0.40, 0.15, 0.90, 0.25, 0.60)     # Fractional canopy cover (0 to 1)
visitor_density <- c(12.5, 45.0, 8.0, 3.5, 62.0, 28.0)       # Visitors per hectare per day
road_dist_km    <- c(4.2, 0.8, 12.0, 6.5, 0.3, 1.9)          # Distance to nearest road
is_peatland     <- c(FALSE, FALSE, TRUE, TRUE, FALSE, FALSE)  # Peat soils have unique acidity
```

#### Part A: Patch Vulnerability Index Function

Write a function named `calculate_patch_vulnerability` that calculates
a threat index (V, defined below) for a single site:

```R
calculate_patch_vulnerability <- function(canopy, visitors, road_dist, peatland){
    # your code here
}

```

The ecological scoring rules are:

1. **Precondition Checks:**
    * Verify all inputs are single values of the correct class (numeric for the first three, logical for `peatland`).
    * Verify that `0 <= canopy <= 1`.
    * Verify that `visitors >= 0` and `road_dist >= 0`.
2. **Base Risk Calculation:**
    * Open canopies receive more light, accelerating invasive plant germination:
      `light_score = 1.0 - canopy`
    * Roads act as seed dispersal corridors; closer roads increase vulnerability:
      `corridor_risk = 1.0/(1.0 + road_dist)`
    * Base vulnerability combines light and road proximity weighted by
      human traffic:
	  ```
	  V = (0.6 * light_score + 0.4 * corridor_risk) * (1.0 + 0.05 * visitors)
	  ```
3. **Soil Modifier:**
    * If `peatland == TRUE`, the acidic soil suppresses most generalist plant invaders. Apply a 40% reduction (`V_final = 0.60 * V`).
    * Otherwise, `V_final = V`.
4. Return `V_final`.

#### Part B: Reserve Registry and Batch Threat Evaluation

Write a function named `audit_reserve_network` that processes all candidate reserves:

```R
audit_reserve_network <-
    function(names, canopy, visitors, road_dist, peatland, threat_threshold = 0.75){
        # Your code here
    }
```

1. **Defensive Check:**
    * Ensure that all five input vectors have the exact same
      length. If not, halt with an appropriate error message.
2. **Iterative Evaluation:**
    * Pre-allocate a numeric vector `vulnerability_scores` of length equal to `length(names)`.
    * Pre-allocate a character vector `action_plan` of the same length.
    * Using a `for` loop over `seq_along(names)`:
        * Call `calculate_patch_vulnerability()` for the i-th site.
        * Store the result in `vulnerability_scores[i]`.
        * Using conditional branching (`if` / `else`):
            * If `vulnerability_scores[i] >= threat_threshold`, assign `"Urgent Weed Eradication"` to `action_plan[i]`.
            * If `vulnerability_scores[i] >= 0.40`, assign `"Routine Monitoring"` to `action_plan[i]`.
            * Otherwise, assign `"Low Priority"` to `action_plan[i]`.
3. **Assemble Summary Data Frame:**
    * Construct and return a data frame with columns:
        * `"reserve"`
        * `"vulnerability"`
        * `"action"`
        * `"high_risk"` (a logical column where `vulnerability >= threat_threshold`)

Run the audit on the 6 protected reserves:

```R
reserve_audit <- audit_reserve_network(
  names            = reserve_names,
  canopy           = canopy_cover,
  visitors         = visitor_density,
  road_dist        = road_dist_km,
  peatland         = is_peatland,
  threat_threshold = 0.75
)

reserve_audit
```

Examine the output data frame. How did the peatland soil modifier affect the vulnerability score of `Cairn_Moss` relative to `Dune_Reach`?

---

### Exercise 2: The Discrete-Time Logistic Map & Non-Linear Population Regimes

A classic foundation of theoretical population biology is Robert May's (1976) analysis of the discrete-time logistic difference equation:
```
x_(t+1) = r x_t (1 - x_t)
```

Here, x_t represents the population density scaled between 0.0 and 1.0 (as a fraction of carrying capacity), and r is the intrinsic reproductive rate (0 < r <= 4.0). 

#### Part A: The Logistic Step Function

Write a function named `logistic_step` that computes a single step:

```R
logistic_step <- function(x, r){
   # Your code here
}
```

1. **Validation:**
    * Verify that both `x` and `r` are single numeric scalars.
    * Verify that 0.0 <= x <= 1.0. If violated, halt with `stop("x must be in [0, 1].")`.
    * Verify that r > 0.0 and r <= 4.0. If violated, halt with `stop("r must be in (0, 4].")`.
2. Compute and return the value of x_(t+1), that is, `r*x*(1 - x)`.

#### Part B: Time-Series Generator

Write a function named `iterate_logistic_map` with the signature:

```R
iterate_logistic_map <- function(x0, r, n_steps){
   # Your code here
}
```

1. Validate that `n_steps` is a single positive integer >= 0.
2. Pre-allocate a numeric vector `trajectory` of length `n_steps + 1`.
3. Set `trajectory[1] <- x0`.
4. Using a `for` loop, iteratively call `logistic_step()` to generate
   values for t = 1, ...,  n\_steps.
5. Return the full `trajectory` vector.

Test your function:
```R
traj <- iterate_logistic_map(x0 = 0.2, r = 2.8, n_steps = 50)
length(traj)    # Should be 51
tail(traj, 5)   # Should settle around ~0.6428
plot(traj)      # Gives you a visual impression
```

#### Part C: Testing for Asymptotic Convergence to a Fixed Point

When small values of r (e.g. r = 2.0), the logistic map converges to a single stable fixed
point. For larger values of r, the value of x starts to oscillate.

Write a function named `check_fixed_point_convergence` that tests whether a trajectory converges:

```R
check_fixed_point_convergence <- function(x0, r, burn_in = 200, tolerance = 1e-4){
    # Your code here
}
```

1. Run the map for `total_steps = burn_in + 2` steps using `iterate_logistic_map()`.
2. Extract the last two elements of the evaluation window and compute
   their absolute difference using the `abs()` function.
3. If this differences is less than `tolerance`:
    * Return `TRUE`.
    * Otherwise, the system is oscillating or chaotic. Return `FALSE`.

Test your convergence checker:
```R
check_fixed_point_convergence(x0 = 0.3, r = 2.6)  
check_fixed_point_convergence(x0 = 0.3, r = 3.2)  
check_fixed_point_convergence(x0 = 0.3, r = 3.8)  
```

---

### Exercise 3 (if you have time): Dynamic Harvest Quotas and Fisheries Escapement

In fisheries conservation, sustainable yield policies often employ **biological escapement thresholds**. Instead of allowing a fixed annual catch, fisheries managers adjust the permitted quota based on the estimated adult biomass at the start of the season.

Consider a coastal cod stock managed under a three-tier harvest control rule:

1. **Moratorium Tier:** If adult biomass B is below the critical
   threshold B\_crit = 1500 tonnes, no fishing is permitted (harvest H = 0).
2. **Proportional Tier:** If biomass is between B\_crit and the target
   threshold B\_targ = 5000 tonnes, harvest is set to:
   ```
   H = 0.20 x (B - B_crit)
   ```
3. **Maximum Sustainable Tier:** If biomass is at or above B\_targ,
   the harvest rate is capped at:
   ```
   H = 0.20 x (B_targ - B_crit) + 0.35 x (B - B_targ)
   ```

Furthermore, under adverse environmental conditions (e.g., poor marine heatwave years), harvest rates must be reduced by an additional conservation buffer.

#### Part A: Single-Season Harvest Decision Function

Write a function named `calc_harvest_quota` that accepts the following arguments:

* `biomass`: Estimated adult biomass in tonnes.
* `marine_heatwave`: Logical scalar indicating whether a severe heatwave occurred during the spawning season (defaults to `FALSE`).
* `b_crit`: Critical threshold (defaults to `1500.0`).
* `b_targ`: Target threshold (defaults to `5000.0`).

The function must execute these steps:

1. **Defensive Validation:**
    * Verify that `biomass` is a single numeric scalar and is strictly non-negative (>= 0). If violated, halt execution with `stop()`.
    * Verify that `marine_heatwave` is a single logical scalar (`TRUE` or `FALSE`).
    * Verify that `b_crit < b_targ`.
2. **Harvest Determination:**
		* Calculate the base harvest H using the three-tier rule above.
    * If `marine_heatwave == TRUE` and H > 0, apply a 25% reduction to the calculated harvest (H\_adj = 0.75 x H).
    * Ensure the final harvest never exceeds the available biomass (H <= B).
3. **Return Value:**
    * Return the final permitted catch as a single numeric scalar.


Test your function with the following calls and check the outputs:

```R
calc_harvest_quota(biomass = 1200)                         # Should return 0
calc_harvest_quota(biomass = 3500)                         # 0.20 * (3500 - 1500) = 400
calc_harvest_quota(biomass = 3500, marine_heatwave = TRUE) # 400 * 0.75 = 300
calc_harvest_quota(biomass = 6000)                         # 0.20 * 3500 + 0.35 * 1000 = 1050
```

#### Part B: Multi-Year Stock Trajectory Simulation

Write a second function named `simulate_fishery_trajectory` with the signature:

```R
simulate_fishery_trajectory <-
    function(b_start, years, intrinsic_growth = 0.35, carrying_capacity = 8000){
        ...
    }
```

The function simulates population changes from year t to year t+1
using a logistic growth model with harvest:
```
B_(t+1) = B_t + r B_t (1 - B_t/K ) - H_t
```
where r = intrinsic\_growth, K = carrying\_capacity, and H_t is determined by calling `calc_harvest_quota(biomass = B_t)`.

Requirements:

1. Validate that `years` is a single integer-like numeric value >= 1.
2. Pre-allocate a numeric vector named `biomass_history` of length `years + 1`. Set `biomass_history[1] <- b_start`.
3. Pre-allocate a numeric vector named `harvest_history` of length `years`.
4. Using a `for` loop over years 1, ..., years:
    * Determine H_t from the current stock level using `calc_harvest_quota()`. Store it in `harvest_history[t]`.
    * Compute the post-harvest biomass at year t+1.
    * **Extinction check:** If calculated biomass falls below 1.0 tonne, set the biomass to 0.0, record the remaining years as 0.0, and terminate the loop immediately using `break`.
5. Package the results into a named list containing:
    * `biomass`: The full trajectory vector.
    * `harvest`: The vector of catches.
    * `collapsed`: A logical flag indicating whether the stock collapsed to zero.
6. Return the list.

Verify your function by running a 20-year simulation starting at B_0 = 2000:

```R
sim_run <- simulate_fishery_trajectory(b_start = 2000, years = 20)
sim_run$collapsed
head(sim_run$biomass)
## Visualise:
plot(sim_run$biomass, xlab = "Year", ylab = "Biomass (tonnes)", 
     ylim=c(0,max(sim_run$biomass)))
points(sim_run$harvest, pch = '-', col = "red")
legend(x = "bottomright", col="red", pch='-', legend = "Harvest")
```

Can you find conditions under which the fishery would collapse based
on the prescribed rules?
