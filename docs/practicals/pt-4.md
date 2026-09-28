# **Practical 4 - Randomness, Sampling, and Stochastic Simulation**

## Introduction

In Practicals 1 to 3, all your calculations were **deterministic**: given the same inputs, your code produced the exact same outputs every single time. 

In nature, however, ecological and environmental processes are inherently **stochastic**. Ecological surveys capture random samples from wider populations; individual organisms face probabilistic risks of mortality, predation, or disease; and environmental drivers fluctuate unpredictably.

Textbooks often treat probability as a collection of abstract formulas. In computational data science, however, the computer is an experimental laboratory where you can observe, manipulate, and experience randomness firsthand. In this practical, you will learn to:

1. **Generate pseudo-random variates** using continuous distributions (`runif`, `rnorm`) and discrete sampling (`sample`, `sample.int`).
2. **Control simulation reproducibility** using random number seeds (`set.seed`).
3. **Model probabilistic events** across entire vectors without loops (e.g., survival, detection, extirpation).
4. **Build intuition for rare events and waiting times**: how many trials does it actually take for an event with probability 0.05 to occur?
5. **Physically experience the difference between an expectation value and a sample mean**: watching independent, noisy histories converge under the Law of Large Numbers.
6. **Compare visual representations of probability**: frequency histograms versus Empirical Cumulative Distribution Functions (`ecdf`).

---

## Generating Random Variates in R

R includes built-in pseudo-random number generators for all standard probability distributions. By convention, random number generating functions begin with the letter `r` (for **r**andom).

### Continuous Uniform Distribution: `runif()`

The uniform distribution models situations where any real number within a given interval `[min, max]` is equally likely to be selected:

```R
runif(n, min = 0, max = 1)
```

* `n`: Number of random numbers to generate.
* `min`: Lower limit (defaults to 0).
* `max`: Upper limit (defaults to 1).

```R
# Simulate 5 random microclimate humidity fractions between 0.30 and 0.85:
runif(n = 5, min = 0.30, max = 0.85)
```

If you omit `min` and `max`, R defaults to drawing numbers uniformly from the unit interval `[0, 1]`.

---

### Continuous Normal Distribution: `rnorm()`

The normal (Gaussian) distribution produces a bell-shaped curve centered around a specified mean, with spread controlled by its standard deviation:

```R
rnorm(n, mean = 0, sd = 1)
```

* `n`: Number of random observations.
* `mean`: Central expectation (defaults to 0).
* `sd`: Standard deviation, defining spread (defaults to 1; must be non-negative).

```R
# Simulate adult body lengths (cm) for 6 individual trout (mean = 28.5 cm, sd = 3.2 cm):
rnorm(n = 6, mean = 28.5, sd = 3.2)
```

---

### Discrete Sampling and Random Permutations: `sample()` and `sample.int()`

To pick elements randomly from a discrete set or vector, use `sample()`:

```R
sample(x, size, replace = FALSE, prob = NULL)
```

* `x`: A vector of elements from which to choose.
* `size`: Number of items to pick.
* `replace`: A logical scalar. If `FALSE` (the default), elements are drawn **without replacement** (each item can be selected at most once). If `TRUE`, elements are drawn **with replacement** (the same item can be picked multiple times).
* `prob`: Optional vector of probability weights if elements are not equally likely.

```R
# Simulate drawing 4 tree counts from discrete values between 5 and 20 WITH replacement:
sample(x = 5:20, size = 4, replace = TRUE)

# Randomly select 3 field plots from 10 candidate sites WITHOUT replacement:
site_ids <- paste0("Site_", 1:10)
sample(x = site_ids, size = 3, replace = FALSE)
```

If you only need to sample integers from `1` to `n`, the optimized function `sample.int(n, size, ...)` is faster and concise:

```R
# Pick a single random integer between 1 and 100:
random_station <- sample.int(100, size = 1)
random_station
```

---

### Reproducibility and the Random Seed: `set.seed()`

Computers cannot produce genuinely random numbers; they use deterministic mathematical algorithms (pseudo-random number generators) that start from an initial number called a **seed**.

If you do not specify a seed, R initializes its generator from your computer's system clock, giving different numbers on every run. When writing reproducible reports, testing scripts, or preparing unit tests, you can fix the seed using `set.seed()`:

```R
set.seed(42)
runif(3)
# [1] 0.9148060 0.9370754 0.2861395

# Running it again produces different numbers:
runif(3)
# [1] 0.8304476 0.6417455 0.5190959

# Resetting the seed reproduces the exact sequence:
set.seed(42)
runif(3)
# [1] 0.9148060 0.9370754 0.2861395
```

> **Rule for Simulations:** Use `set.seed()` at the top of your final script when you need identical, reproducible output for an assessment or publication. During exploratory testing, run without a fixed seed to confirm your code behaves properly across different random realizations.

#### Q1. Practice with Random Generators
1. Write an expression to generate 15 random numbers drawn uniformly between 10.0 and 50.0.
2. Write an expression that samples 5 whole numbers between 1 and 50 **without replacement**. What happens if you request `size = 60` with `replace = FALSE`?
3. Generate 8 values from a normal distribution with `mean = 100` and `sd = 15`.

---

## Simulating Random Events with Vectorized Expressions

In ecological models, we frequently need to decide whether a discrete event occurs for each member of a population (e.g., does an individual disperse? does a seed germinate? does a patch experience extirpation?).

### The Uniform Threshold Technique

To simulate an event that occurs with probability `p` (where `0 <= p <= 1`), compare a uniform random number in `[0, 1]` against `p`:

```
Event occurs if: runif(1) < p
```

Because `runif(1)` is distributed evenly between 0 and 1, the probability that it falls below `p` is exactly `p`.

Crucially, because relational operators (`<`, `<=`) are fully vectorized in R, you can simulate thousands of independent events simultaneously **without writing a single loop**:

```R
# Suppose we have 8 habitat patches, each with an annual extirpation risk of 0.20:
n_patches <- 8
p_extirpation <- 0.20

# Draw 8 random values:
random_draws <- runif(n_patches)

# Vectorized event check:
went_extinct <- random_draws < p_extirpation
went_extinct
# [1] FALSE FALSE  TRUE FALSE FALSE FALSE FALSE FALSE
```

### Heterogeneous Probabilities Across Individuals

This vectorized pattern works equally well when each individual or patch has its own unique risk probability:

```R
# 5 individual fish, each with a different predation risk based on body size:
predation_risk <- c(0.10, 0.45, 0.05, 0.80, 0.30)

# Simulate predation events in a single vectorized step:
eaten <- runif(length(predation_risk)) < predation_risk
eaten
```

#### Q2. Vectorized Survival
A population of 100 seeds is planted in a restoration project. Each seed has an independent germination probability of 0.65.
1. Using a single vectorized expression (no loops), generate a logical vector of length 100 indicating which seeds germinated.
2. How many seeds in your simulation germinated? (Hint: `sum()` counts `TRUE` entries).

---

## Experiencing Randomness

Probabilistic intuition is notoriously deceiving. We tend to expect random processes to look neatly structured or evenly spread out, and we underestimate how much natural clumpiness, streaking, and fluctuation occurs purely by chance.

Let us explore four fundamental phenomena through direct computational experimentation.

---

### 1. The Waiting Game: How Long Until Something with p = 0.05 Happens?

In experimental science and statistical hypothesis testing, an event with probability `p = 0.05` is often considered "rare" (occurring on average 1 in 20 times). 

But what does this mean in practice?  Let's find out.

First execute the code below a few times. It outputs TRUE in 5% of cases.

```R
runif(1) < 0.05
```

Now, let's automate this.  Let's write a `while` loop that counts how
many independent trials it takes until a 'rare' event (p = 0.05)
occurs for the first time:

```R
trial_count <- 0
event_occurred <- FALSE

while (!event_occurred) {
  trial_count <- trial_count + 1
  # Test if event occurs on this trial:
  if (runif(1) < 0.05) {
    event_occurred <- TRUE
  }
}

cat("Event occurred on trial number:", trial_count, "\n")
```

Run that code block in your console 5 to 10 times in
succession. Notice the dramatic unpredictability:

* In one run, the event might strike on trial 2 or 3.
* In another run, you might wait 45, 60, or even more than 100 trials without seeing it occur.

#### Repeated Simulation: The Waiting-Time Distribution

To see the full pattern, let us automate this experiment across 1,000 independent simulated runs:

```R
simulate_waiting_time <- function(p = 0.05) {
  steps <- 0
  while (TRUE) {
    steps <- steps + 1
    if (runif(1) < p) {
      return(steps)
    }
  }
}

# Run 1000 independent replications:
n_reps <- 1000
waiting_times <- rep(NA, n_reps)

for (i in seq_len(n_reps)) {
  waiting_times[i] <- simulate_waiting_time(p = 0.05)
}

# Plot waiting times across all 1000 successive runs:
plot(waiting_times, pch = 16, col = "grey50",
     xlab = "Simulation Run", ylab = "Trials Until First Event",
     main = "Waiting Times Across 1000 Independent Realizations")
abline(h = 20, col = "red", lty = 2, lwd = 2)  # Theoretical average wait (1 / 0.05)
legend("topright", legend = "Expected Mean (20 trials)", col = "red", lty = 2, lwd = 2)
```

Look at the plot:

  * Each point represents one complete simulation run. Notice the dense
  cloud of points hugging the bottom axis: in a large proportion of
  runs, the event occurred within the first 5 to 15 trials.  
  * More than half of the runs finish **below** the theoretical average wait of 20 trials (the red dashed line).
  * At the same time, look at the erratic spikes shooting high above the
  average: occasionally, you wait 60, 80, or even more than 100 trials!

Now, let us group these 1,000 individual runs into a frequency histogram to view the aggregated distribution:

```R
# Visualise the distribution of waiting times:
hist(waiting_times, breaks = 30, col = "blue", 
     xlab = "Number of trials until first event",
     main = "Waiting Times for p = 0.05 Event")
abline(v = mean(waiting_times), col = "red", lwd = 2, lty = 2)
legend("topright", legend = paste("Mean =", round(mean(waiting_times), 1)), 
       col = "red", lty = 2, lwd = 2)
```

> **Takeaway:** Randomness exhibits so-called **geometric waiting
> times**. Even when the expected average wait is 20 trials, waiting
> times are heavily skewed.  Something that happens at random with 5%
> probability easily happens quite soon or after quite a long time.

---

### 2. Expectation vs. Mean: Discovering the Invisible Attractor of an Unknown System

In introductory textbooks, probability and expectation are often presented using symmetrical objects like fair coins or fair dice. But this makes the concept of expectation deceptively trivial: if a die has faces 1 through 6, people simply calculate the arithmetic average `(1 + 2 + 3 + 4 + 5 + 6) / 6 = 3.5` and assume that is all there is to it.

In real nature, however, **there are no symmetric dice**.

* Organisms do not have equal probabilities of surviving or dying.
* Seeds do not have equal chances of dispersing to every location.
* We **never** know the underlying probabilities in advance.

Furthermore, defining probability and expectation in words quickly leads to circular reasoning: we define probability as the expected relative frequency over infinite trials, and we define expectation as the probability-weighted sum of outcomes. You cannot define either word without already relying on the other!

The only way to break out of this circle and understand what an **expectation value** truly is—and how it fundamentally differs from a **sample mean**—is to experience it computationally on an unknown, loaded system.

#### Building a "Mystery Loaded Die" using an R Closure

In R, a **closure** is a function that carries its own private, enclosed environment. Variables stored inside a closure remain sealed away from your global workspace: you cannot see them in your RStudio Environment panel, and you cannot accidentally tamper with them.

We can use a closure to create a unique "mystery die" for each student. Run the following code to define the generator factory:



```R
create_mystery_die <- function(student_id) {
  # Save current random state so this setup does not alter global randomness:
  old_seed <- if (exists(".Random.seed", envir = .GlobalEnv)) get(".Random.seed", envir = .GlobalEnv) else NULL
  
  # Initialize secret, non-uniform probabilities based on the student's ID:
  set.seed(student_id)
  weights <- runif(6, min = 0.5, max = 1.5)
  secret_probs <- weights / sum(weights)
  secret_expectation <- sum((1:6) * secret_probs)
  
  # Restore the prior global random state:
  if (!is.null(old_seed)) assign(".Random.seed", old_seed, envir = .GlobalEnv)
  
  # Return the mystery die function:
  function(n = 1, reveal = NA) {
      if (!is.na(reveal) &&
          tools::md5sum(bytes = charToRaw(as.character(reveal))) ==
          "a542e9b744bedcfd874129ab0f98c4ff") {
          return(list(probabilities = round(secret_probs, 4), 
                      expectation   = round(secret_expectation, 4)))
      }
      sample(1:6, size = n, replace = TRUE, prob = secret_probs)
  }
}
```
[comment]: <> (password: "knowledge")

Now, instantiate your personal die by supplying your student ID number (or any unique integer of your choice):

```R
# Replace 12345 with your actual student ID number:
my_die <- create_mystery_die(student_id = 12345)
```

Look at your RStudio **Environment** panel. `my_die` is simply listed as a function. The underlying probabilities are locked inside. 

Roll your mystery die a few times:

```R
my_die(1)   # Roll once
my_die(10)  # Roll 10 times
```

#### The Noisy Sample Mean

What is the "average" outcome of your die? Because the die is loaded with secret weights, you cannot calculate `(1 + ... + 6) / 6`. 

Let us compute the sample mean across a small sample of 15 rolls:

```R
mean(my_die(15))
```

Run that line three or four times in your console. Notice how erratic the result is:
* In one run, you might get `4.2`.
* In the next, you might get `2.8`.
* In another, `3.5`.

The **sample mean** (`mean(x)`) is an empirical calculation from real, collected data. It is noisy, erratic, and changes on every single survey. If you were a field ecologist with a small sample size (`n = 15`), you would have no idea what the true underlying property of this system actually is.

#### Watching Multiple Parallel Universes Converge

If the expectation value cannot be reliably observed in a single sample, how does it physically manifest?

Let us simulate rolling your mystery die 2,000 times. But instead of watching just one sequence, let us simulate **5 independent parallel universes** running side by side, and plot their running sample means as data accumulates:

```R
n_rolls <- 2000
n_universes <- 5

# Set up an empty coordinate space:
plot(NA, xlim = c(1, n_rolls), ylim = c(1.5, 5.5),
     xlab = "Number of Die Rolls (Sample Size n)",
     ylab = "Cumulative Running Mean",
     main = "5 Independent Realizations of Your Mystery Die")

# Assign distinct colours to each parallel history:
palette <- c("steelblue", "forestgreen", "darkorange", "purple", "firebrick")

# Pre-allocate a vector to record the final asymptotic estimates:
final_estimates <- numeric(n_universes)

# Simulate and overlay each running trajectory:
for (k in seq_len(n_universes)) {
  rolls <- my_die(n = n_rolls)
  running_mean <- cumsum(rolls) / seq_len(n_rolls)
  lines(running_mean, col = palette[k], lwd = 1.4)
  final_estimates[k] <- running_mean[n_rolls]
}
```

Examine this plot carefully. It captures the physical essence of what statisticians call the **Law of Large Numbers**:

1. **The Early Fan of Chaos (`n < 50`):**
   Look at the left edge of the plot. Each universe lives in its own wild reality. In one universe, early high rolls push the running mean up; in another, early low rolls drag it down. If you stopped sampling here, you would conclude that the universes follow completely different laws of nature.
2. **The Inevitable Collapse into the Center of Gravity (`n > 500`):**
   Now follow the lines to the right as `n` crosses 500, 1,000, and 2,000 rolls. 
   None of these five simulations have any awareness of each other. The random number generator does not "remember" past rolls to balance out bad luck. Yet, despite being completely independent and random, **all five lines are inexorably funneled into the exact same horizontal asymptote**.
3. **Compare with Your Neighbour:**
   Look at the screen of the student sitting next to you. Their five lines *also* converged into a razor-thin horizontal beam—but their beam converged at a **completely different number** from yours!

#### Revealing the Secret Expectation

Estimate where your five parallel histories converged by averaging their final endpoints:

```R
cat("Empirical estimate of the attractor:", round(mean(final_estimates), 3), "\n")
```

Now, unlock the closure and reveal the true mathematical properties baked into your die:

```R
truth <- my_die(reveal = TRUE)
truth
```

Overlay this true expectation onto your plot as a dashed black line:

```R
abline(h = truth$expectation, col = "black", lty = 2, lwd = 2.5)
legend("topright", 
       legend = c("Independent Sample Histories", paste("True Expectation =", truth$expectation)),
       col = c("steelblue", "black"), lty = c(1, 2), lwd = c(1.4, 2.5))
```

Look at how the dashed line cuts directly through the convergence channel.

Reflect deeply on what you have just witnessed:
* The **sample mean** is an empirical measurement. It lives in the data. It is noisy and changes with every sample.
* The **expectation value** is **not** an average of data. It was fixed the moment `create_mystery_die` was executed—*before a single die was ever rolled*.
* The expectation value is the invisible **center of gravity** of the generating mechanism. The Law of Large Numbers is the physical guarantee that as individual random observations accumulate, their collective historical average is inexorably captured by this invisible attractor.

#### Zooming In: The Small-Sample Trap

To understand why field biology and conservation surveys are so difficult, zoom into the first 100 rolls of your experiment:

```R
# Re-plot focusing strictly on small sample sizes (n = 1 to 100):
plot(NA, xlim = c(1, 100), ylim = c(1.5, 5.5),
     xlab = "Number of Die Rolls (Sample Size n)",
     ylab = "Cumulative Running Mean",
     main = "The Small-Sample Trap: First 100 Rolls")
abline(h = truth$expectation, col = "black", lty = 2, lwd = 2)

for (k in seq_len(n_universes)) {
  rolls <- my_die(n = 100)
  running_mean <- cumsum(rolls) / seq_len(100)
  lines(running_mean, col = palette[k], lwd = 1.4)
}
```

Notice how wide the spread remains even at `n = 50`. When researchers report "the average body mass is 3.2 kg" from a field survey of 30 animals, they often assume their sample mean accurately reflects the underlying expectation. As your plot demonstrates, that intuition is frequently overconfident.

#### Q3. Testing the Attractor of a Continuous Uniform Distribution
If a variable is drawn uniformly from continuous values between 10.0 and 20.0 (`runif(n, min = 10.0, max = 20.0)`), every real value in that interval is equally likely.
1. What is the theoretical expectation (center of gravity) of this continuous distribution?
2. Write a script that simulates 4 independent histories of 1,500 uniform draws between 10.0 and 20.0, and plots their running means simultaneously on a single graph.
3. Overlay a horizontal dashed line at the theoretical expectation. At approximately what sample size `n` do all 4 independent histories consistently stay within `+/- 0.2` of the expectation?

---

### 3. Variance, Standard Deviation, and the 1 / sqrt(n) Convergence Rule

While the sample mean converges to the true expectation, **individual observations do not become less variable**.

Let us generate samples of varying sizes from a standard normal distribution (`mean = 0`, `sd = 1`):

```R
sample_small <- rnorm(20, mean = 0, sd = 1)
sample_large <- rnorm(5000, mean = 0, sd = 1)

sd(sample_small)  # Might be 0.82 or 1.18 (noisy estimate)
sd(sample_large)  # Very close to 1.00
```

Notice the crucial difference:
* **Standard Deviation (`s`):** Measures the spread of the underlying population. It remains approximately constant (`~1.0`) regardless of how many data points you collect.
* **Standard Error of the Mean (`SE = s / sqrt(n)`):** Measures the uncertainty in the estimated mean. It shrinks proportionally to `1 / sqrt(n)`.

To cut your estimation uncertainty in half, you must collect **four times** as much data (`2^2 = 4`).

---

### 4. Histograms vs. Empirical Cumulative Distribution Functions (ECDF)

When visualizing continuous random data, researchers typically reach for a **histogram**. However, histograms have a serious drawback: their visual shape is sensitive to arbitrary bin boundaries.

A complementary tool is the **Empirical Cumulative Distribution Function (ECDF)**. The ECDF at any point `x` shows the exact fraction of observations in the data that are less than or equal to `x`:

```
F_n(x) = (Number of observations <= x) / n
```

Let us compare both views on a sample of 200 normal variates:

```R
set.seed(101)
sim_data <- rnorm(200, mean = 15.0, sd = 3.0)

# Set up side-by-side plot layout:
par(mfrow = c(1, 2))

# 1. Frequency Histogram:
hist(sim_data, breaks = 15, col = "lightgrey", 
     main = "Histogram (Binned Density)", xlab = "Measured Value")

# 2. Empirical Cumulative Distribution Function:
plot(ecdf(sim_data), verticals = TRUE, col = "navy",
     main = "Empirical Cumulative Distribution",
     xlab = "Measured Value", ylab = "Cumulative Proportion <= x")
abline(h = 0.5, col = "red", lty = 3)  # Marks the 50th percentile (median)
abline(v = 15.0, col = "red", lty = 3)

# Reset plotting layout to single panel:
par(mfrow = c(1, 1))
```

Reading the ECDF:
* Look at the y-axis: the curve rises smoothly from 0.0 (minimum value) to 1.0 (maximum value).
* At the median (`x ~ 15.0`), exactly half of the cumulative probability (`y = 0.50`) has accumulated.
* Steep sections in the ECDF indicate regions where data points are densely clustered; flat sections indicate sparse regions.

#### Q4. Inspecting the ECDF
Using `sim_data` from above:
1. Use `mean(sim_data <= 12.0)` to calculate the exact proportion of data points that are `<= 12.0`.
2. Verify where this point falls on the vertical axis of your ECDF plot.

---

## Practical Review Exercises

Work through these multi-step exercises in your R script. Focus on combining your custom functions, loop structures, and vectorized operations with the stochastic tools introduced today.

---

### Exercise 1: The Mark-Release-Recapture Lincoln-Petersen Simulator

In wildlife biology, ecologists estimate the size of an elusive animal population (`N_true`) using the **Lincoln-Petersen mark-recapture method**:

1. In the first sampling session, `M` animals are captured, marked, and released back into the wild.
2. After allowing time for marked animals to mix into the population, a second sample of `C` animals is captured.
3. The number of marked individuals recaptured in the second sample is recorded as `R`.
4. The estimated total population size (`N_hat`) is calculated as:
   ```
   N_hat = (M * C) / R
   ```

Because recapture is a stochastic process, `R` fluctuates randomly across surveys, causing `N_hat` to vary.

#### Part A: Simulating a Single Recapture Survey

Write a function named `simulate_recapture_survey` with the signature:

```R
simulate_recapture_survey <- function(n_true, m_marked, c_captured) {
  # Your code here
}
```

The function must execute these steps:

1. **Defensive Validation:**
    * Verify that `n_true`, `m_marked`, and `c_captured` are single numeric integers >= 1.
    * Verify that `m_marked <= n_true` and `c_captured <= n_true`. If violated, halt with `stop()`.
2. **Population Representation:**
    * Represent the population of size `n_true` as an integer vector: marked individuals are represented by `1`, and unmarked individuals by `0`.
    * Construct this vector using `rep()`:
      ```R
      population <- c(rep(1, m_marked), rep(0, n_true - m_marked))
      ```
3. **Simulating Capture:**
    * Draw a sample of size `c_captured` from `population` **without replacement** using `sample()`.
    * Count how many marked individuals were recaptured by summing the sample (`R`).
4. **Estimation:**
    * If `R > 0`, compute `N_hat = (M * C) / R`.
    * If `R == 0` (no marked animals were recaptured), the formula divides by zero; return `NA`.
5. Return `N_hat` as a single numeric scalar.

Test your function with a known population:

```R
simulate_recapture_survey(n_true = 500, m_marked = 80, c_captured = 60)
```

Run this line three or four times. Notice that even though the true population is fixed at 500, the estimate fluctuates.

#### Part B: Evaluating Estimator Bias and Variance

Now, write an iterated simulation script to evaluate how well the Lincoln-Petersen estimator performs:

1. Fix the parameters: `n_true = 1000`, `m_marked = 150`, and `c_captured = 100`.
2. Pre-allocate a numeric vector named `pop_estimates` of length 500.
3. Using a `for` loop, run `simulate_recapture_survey()` 500 times, storing each returned estimate in `pop_estimates`.
4. Calculate the proportion of surveys that failed due to zero recaptures (`mean(is.na(pop_estimates))`).
5. Compute the average population estimate across all valid surveys (`mean(pop_estimates, na.rm = TRUE)`). How close is this to the true value of 1,000?
6. Plot a histogram of the valid estimates and overlay a vertical red line marking the true population size (`1000`).

---

### Exercise 2: Forest Gap Disturbance & Tree Recruitment Dynamics

In temperate rainforests, canopy trees persist for centuries until catastrophic windthrow storms create light gaps, triggering new sapling recruitment.

We will simulate a stand of 100 canopy trees over annual time steps.

#### Problem Formulation

* **Stand State:** The stand is represented by a numeric vector `tree_ages` of length 100, where each element is the age of the tree occupying that canopy position.
* **Annual Disturbance Risk:** In any given year, an extreme windstorm occurs with probability `p_storm = 0.08`.
* **Tree Mortality:**
    * If a windstorm occurs in year `t`, older trees are vulnerable to windthrow: any tree with `age > 60 years` suffers an 80% mortality rate (each older tree dies independently with probability 0.80).
    * Younger trees (`age <= 60`) have flexible trunks and survive the storm.
    * In years without a storm, trees simply age by 1 year.
* **Gap Regeneration:** When a tree dies, its position is immediately colonized by a new seedling of `age = 1 year`.

#### Tasks

1. Write a function named `simulate_forest_year` that updates the stand across a single annual time step:
   ```R
   simulate_forest_year <- function(tree_ages, p_storm = 0.08) {
     # Your code here
   }
   ```
    * Validate that `tree_ages` is a numeric vector with length >= 1 and contains no negative values.
    * Check if a storm occurs this year by testing `runif(1) < p_storm`.
    * If a storm occurs:
        * Identify indices of vulnerable trees where `tree_ages > 60`.
        * For each vulnerable tree, determine if it dies using vectorized random uniform numbers: `runif(n_vulnerable) < 0.80`.
        * Reset the age of trees that died to `1`.
        * For all trees that survived, increase their age by `1`.
    * If no storm occurs:
        * Increment the age of all trees by `1`.
    * Return the updated `tree_ages` vector.

2. Write a multi-year simulation function named `run_forest_simulation`:
   ```R
   run_forest_simulation <- function(initial_ages, n_years, p_storm = 0.08) {
     # Your code here
   }
   ```
    * Pre-allocate a numeric vector named `mean_age_trajectory` of length `n_years + 1`.
    * Record the initial mean stand age in `mean_age_trajectory[1]`.
    * Using a `for` loop over `1:n_years`, iteratively update the forest stand and record the mean tree age at each step.
    * Return a named list containing:
        * `final_stand`: The vector of tree ages at the end of the simulation.
        * `mean_ages`: The annual trajectory of mean stand age.

3. Run a 150-year simulation starting from an even-aged mature stand:
   ```R
   set.seed(456)
   initial_stand <- rep(50, 100) # 100 trees, all 50 years old
   
   sim_results <- run_forest_simulation(initial_ages = initial_stand, n_years = 150, p_storm = 0.08)
   
   # Plot mean stand age trajectory over time:
   plot(sim_results$mean_ages, type = "l", col = "forestgreen", lwd = 2,
        xlab = "Year", ylab = "Mean Canopy Age (years)",
        main = "Forest Stand Age Under Stochastic Storm Disturbance")
   ```

Examine the trajectory plot. Why does the mean age show gradual upward climbs punctuated by sudden sharp drops?

---

### Exercise 3: Empirical Quantiles and the 5% Rare Event Risk

In environmental management, the "1-in-20 year" flood or storm corresponds to an annual exceedance probability of `p = 0.05`. 

When planning infrastructure or conservation reserves, stakeholders often mistakenly assume that a 1-in-20 year event will happen roughly once every 20 years. In reality, multiple rare events can strike in quick succession, or a site may experience decades of calm.

#### Tasks

1. Write a function named `sample_events_in_horizon` that simulates the total number of rare events observed over a 20-year operational planning horizon:
   ```R
   sample_events_in_horizon <- function(horizon_years = 20, p_annual = 0.05) {
     # Draw horizon_years uniform numbers and count how many are < p_annual
     event_count <- sum(runif(horizon_years) < p_annual)
     return(event_count)
   }
   ```

2. Run an iterated simulation:
    * Execute `sample_events_in_horizon()` 2,000 times.
    * Store the resulting counts in a numeric vector named `observed_counts`.

3. Analyze the empirical risk profile across the 2,000 simulated 20-year horizons:
    * What proportion of 20-year periods experienced **exactly zero** rare events? (`mean(observed_counts == 0)`)
    * What proportion experienced **two or more** rare events? (`mean(observed_counts >= 2)`)
    * What was the maximum number of rare events observed in a single 20-year window?

4. Plot a bar plot or frequency histogram of `observed_counts`:
   ```R
   counts_table <- table(observed_counts) / length(observed_counts)
   barplot(counts_table, col = "salmon",
           xlab = "Number of Events Experienced in 20 Years",
           ylab = "Empirical Probability",
           main = "Realised Frequency of a '1-in-20 Year' (p = 0.05) Event")
   ```

Reflect on your findings: over a 20-year window, are you more likely
to experience *zero* events, or *at least one* event? How does this
computational experiment change your interpretation of a "1-in-20
year" risk?
