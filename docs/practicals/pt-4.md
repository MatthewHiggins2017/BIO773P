# **Practical 4 - Randomness, Sampling, and Stochastic Simulation**

## Introduction

In Practicals 1 to 3, all your calculations were **deterministic**: given the same inputs, your code produced the exact same outputs every single time. 

In nature, however, ecological and environmental processes are inherently **stochastic**. Ecological surveys capture random samples from wider populations; individual organisms face probabilistic risks of mortality, predation, or disease; and environmental drivers fluctuate unpredictably.

Textbooks often treat probability as a collection of abstract formulas. In computational data science, however, the computer is an experimental laboratory where you can observe, manipulate, and experience randomness firsthand. In this practical, you will:

1. **Generate pseudo-random variates** using continuous distributions (`runif`, `rnorm`) and discrete sampling (`sample`, `sample.int`).
2. **Control simulation reproducibility** using random number seeds (`set.seed`).
3. **Model probabilistic events** across entire vectors without loops (e.g., survival, detection, extirpation).
4. **Build intuition for rare events and waiting times**: how many trials does it actually take for an event with probability 0.05 to occur?
5. **Experiment with the difference between an expectation value and a sample mean**: watching independent, noisy histories converge under the Law of Large Numbers.
6. **Apply representations of probability distribution**: frequency histograms versus Empirical Cumulative Distribution Functions.
6. **See how the Central Limit Theorem in action**: the near-universal
   distribution of means.

---

## 1. Generating Random Variates in R

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
# Simulate 5 random microclimate humidity proportions between 0.30 and 0.85:
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

## 2. Reproducibility and the Random Seed: `set.seed()`

Computers cannot produce genuinely random numbers; they use deterministic mathematical algorithms (pseudo-random number generators) that start from an initial number called a **seed**.

If you do not specify a seed, R initializes its generator from your
computer's system clock, giving different numbers on every run. When
writing reproducible reports or testing scripts, you can fix the seed using `set.seed()`:

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

> **Rule for Simulations:** Use `set.seed()` at the top of your final script when you need identical, reproducible output for a publication. During exploratory testing, run without a fixed seed to confirm your code behaves properly across different random realizations.

#### Q1. Practice with Random Generators
1. Write an expression to generate 15 random numbers drawn uniformly between 10.0 and 50.0.
2. Write an expression that samples 5 whole numbers between 1 and 50 **without replacement**. What happens if you request `size = 60` with `replace = FALSE`?
3. Generate 8 values from a normal distribution with `mean = 100` and `sd = 15`.

---

## 3. Simulating Random Events with Vectorized Expressions

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
got_extirpated <- random_draws < p_extirpation
got_extirpated
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
A population of 100 seeds is planted in a restoration project. Each
seed has an independent germination probability of 0.65.

1. Using a single vectorized expression (no loops), generate a logical vector of length 100 indicating which seeds germinated.
2. How many seeds in your simulation germinated? (Hint: `sum()` counts `TRUE` entries).

---

## 4. Experiencing Randomness

Probabilistic intuition is notoriously deceiving. We tend to expect random processes to look neatly structured or evenly spread out, and we underestimate how much natural clumpiness, streaking, and fluctuation occurs purely by chance.

Let us explore some fundamental phenomena through direct computational experimentation.

---

### The Waiting Game: How Long Until Something with p = 0.05 Happens?

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
succession. Notice the dramatic unpredictability.

#### Repeated Simulation: The Waiting-Time Distribution

To see the full pattern, let us automate this experiment across 1,000 independent simulated runs:

```R
simulate_waiting_time <- function(p = 0.05) {
  trial_count <- 0
  while (TRUE) {
    trial_count <- trial_count + 1
    if (runif(1) < p) {
      return(trial_count)
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
plot(waiting_times, pch = 16, col = "grey",
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

## 5. Expectation vs. Mean:  The Invisible Attraction of Expectation

After having built intuition about the concept of "probability", let's
develop intuition about another concept that is central to statistics:
the "expectation value".  Conceptually, the expectation value of a
random variable (such as the outcome of a measurement) is the average
over a very large number of equivalent measurements. But what does
this really mean?

### Building a "Mystery Loaded Die"

Copy the code below and into your RStudio script and evaluate it to
define the function `create_mystery_die`.  

```R
create_mystery_die <- function(n_faces = 6) {
  ## This function outputs a mystery die with n_faces faces.
  if(!is.numeric(n_faces) || is.na(n_faces[1]) || n_faces[1] < 2)
    stop("Bad value for n_faces.")
  n_faces <- as.integer(n_faces[1])
  randomness  <- runif(1)
  set.seed(as.numeric(Sys.time()) * randomness)
  weights <- runif(n_faces, min = 0.5, max = 1.5)
  secret_probs <- weights / sum(weights)
  secret_expectation <- sum((1:n_faces) * secret_probs)
  set.seed(randomness * .Machine$integer.max)
  function(n = 1) {
    sample(1:n_faces, size = n, replace = TRUE, prob = secret_probs)
  }
}
```

The function uses some advanced R trickery and I won't explain _how_
it works.  But _what_ it does is the following:

Calling create_mystery_die() returns another function. This function
simulates a loaded die, that is, a die that will give you one of the
outcomes 1, 2, 3, 4, 5, 6 each time you 'roll' it, but it won't give
them with the exact same probability. _Nobody_, not even I, knows what
the probability of each outcome is!

> This is the situation that almost _every_ scientist doing empirical
> research faces. You will face it as well when doing your MSc
> project: when you collect data or do measurements, your are getting
> random outcomes each time according to rules that _nobody_
> knows. And yet you need to conclude something useful from this
> randomness to submit your thesis!

Now, instantiate your personal die:

```R
my_die <- create_mystery_die()
```

Try out your die:
```R
my_die(1)   # Roll once
my_die(1)   # Roll again
my_die(1)   # Roll another time
my_die(10)  # Roll 10 times
```
Can you see from the outputs that the die is loaded?

### The Sample Mean

For **a die that is not loaded**, we can compute the "average" outcome of
rolling it.  Because all faces of the die have the same probability,
the "average" outcome can be calculate by averaging over the
outcomes: `(1 + ... + 6) / 6 = 3.5`. But, as you know, the outcome of
each roll is random, so simply averaging over six rolls won't give
3.5. We can use the `sample.int()` function to demonstrate this. Try
it out several times:
```R
six_fair_rolls <- sample.int(n = 6, size = 6, replace = TRUE)
six_fair_rolls
mean(six_fair_rolls)
```

The outcome is hard to distinguish from that using a loaded 
die. Compare with the result of running the following code several times:
```R
six_loaded_rolls <- my_die(6)
six_loaded_rolls
mean(six_loaded_rolls)
```

To get, for a fair die, an average close to the value 3.5 that we expect, we need to
average over _many, many_ rolls. Try the following a few times:
```R
mean(sample.int(n = 6, size = 10^6, replace = TRUE))
```
> Here's the crucial observation: by averaging over many unpredictable
> outcomes, we obtained a pretty well reproducible outcome. This
> reproducible outcome from averaging over _many, many_ samples is called
> the **expectation value**.

We can use this method to show that our mystery die is loaded. Try the
following a few time:
```R
mean(my_die(10^6))
```
> You can see that the **expectation value** of rolling `my_die` is
> also reproducible and clearly
> different from that of a fair die.  The difference is that for the
> fair die we could compute the expectation value using some maths.  For
> `my_die` we could not.  We _had_ to obtain it from averaging over many measurements. 
> So, if you'd be studying `my_die`
> for your MSc thesis, you could:
>
>   * Report the expectation value you find.
>   * Observe that the expectation value is clearly different from
>     that for a fair die.
>   * Conclude that `my_die` is not fair, but rather tends to give
>     lower [or higher] values.
>   * However, you'd have to roll the die 10^6 = 1,000,000 times!


### Watching Means in Multiple Parallel Universes Converge

Here's the problem: you usually can't make 10^6 = 1,000,000
measurements for your MSc thesis!

So, how many measurements do we rally need to get hold of an
expectation value?

Let us simulate rolling the mystery die 2,000 times. But instead of
watching just one sequence, let us simulate [**5 independent parallel
universes**](https://en.wikipedia.org/wiki/Everything_Everywhere_All_at_Once) running side by side, and plot the sample means we get as
data accumulates:

```R
n_rolls <- 2000
n_universes <- 5

# Set up an empty coordinate space:
plot(NA, xlim = c(1, n_rolls), ylim = c(1.5, 5.5),
     xlab = "Number of Die Rolls (Sample Size n)",
     ylab = "Cumulative Running Mean",
     main = "5 Independent Realizations of My_Die")

# Assign distinct colours to each parallel history:
palette <- c("lightblue", "darkgreen", "darkorange", "purple", "brown")

# Pre-allocate a vector to record the final asymptotic estimates:
final_estimates <- numeric(n_universes)

# Simulate and overlay each running trajectory:
for (k in seq_len(n_universes)) {
  rolls <- my_die(n = n_rolls)
  # Here we use a trick to compute all n_roll means in one line:
  running_mean <- cumsum(rolls) / seq_len(n_rolls)
  lines(running_mean, col = palette[k], lwd = 1.4)
  final_estimates[k] <- running_mean[n_rolls]
}
```

Write down what you observe:
```R
## Your notes here
```

How many samples do you recommend to take to get reliable estimates of expectation values?
```R
## How many samples:
```

Reflect on what you have just witnessed:

* The **sample mean** is an empirical measurement. It is a random
  number. It is noisy and changes with every sample.
* The **expectation value** is **not** an average of data. It was
  fixed the moment `my_die` was created by `create_mystery_die`,
  *before a single die was ever rolled*.
* The expectation value is like an **invisible attractor** towards
  which sample means move for large sample sizes. The Law of Large
  Numbers is the physical guarantee that, as individual random
  observations accumulate, their collective historical average is
  captured by this invisible attractor.

### Zooming In: Small-Sample Size Experiments

To understand why field biology and conservation surveys are so
difficult, let's do some experiments with only 100 rolls of our die:

```R
# Re-plot focusing strictly on small sample sizes (n = 1 to 100):
plot(NA, xlim = c(1, 100), ylim = c(1.5, 5.5),
     xlab = "Number of Die Rolls (Sample Size n)",
     ylab = "Cumulative Running Mean",
     main = "The Small-Sample Trap: First 100 Rolls")

for (k in seq_len(n_universes)) {
  rolls <- my_die(n = 100)
  running_mean <- cumsum(rolls) / seq_len(100)
  lines(running_mean, col = palette[k], lwd = 1.4)
}
```

Notice how wide the spread remains even at `n = 50`. When researchers
report "the average body mass is 3.2 kg" from a field survey of 30
animals, this may sounds as if the sample mean accurately reflects the
underlying expectation value. As your plot demonstrates, that
intuition can be overconfident.

#### Q3. The Expectation Value of a Continuous Uniform Distribution
If a variable is drawn uniformly from continuous values between 10.0
and 20.0 (`runif(n, min = 10.0, max = 20.0)`), every real value in
that interval is equally likely.

1. What is the expectation value of this continuous distribution?
2. Write a script that simulates 4 independent histories of 1,500
   uniform draws between 10.0 and 20.0, and plots their running means
   (as above) simultaneously on a single graph.
3. Overlay a horizontal line indicating the expectation value using
   `abline(h = [your value here])`. At approximately what sample size
   `n` do all 4 independent histories consistently stay within `+/-
   0.2` of the expectation?

---

## 6. Histograms and Empirical Cumulative Distribution Functions

The expectation value is not the only property of random variables
(such as measured values) that is accessible _only_ by taking many,
many, samples - more than we can typically take in ecology.

The full information about at random variable is given by the
distribution of its values.  There are two ways to think about a
distribution. Mathematicians are interested in what they call the _distribution function_:
```
F(x) = (probability that observation <= x)
```
Practitioners usually ask: what is the probability for my observation
to be in the range between x1 and x2.  We can obtain this from F(x) as
```
(probability that observation > x1 but <= x2) = F(x1) - F(x1).
```
From a limited number of samples, we can estimate the distribution
using either a histogram or the empirical cumulative distribution
(given by the proportion of observations <= x). 

Since these representations convey the same information, we call them
collectively **the distribution** of the observable or random
variable.

#### Q4. Experimenting with empirical distributions
The code below generates both the empirical distribution and the
histogram of some mock random data. 
Experiment with increasing (e.g., by a factor 10 or 100) the values of
`n_samples` and of the `breaks` parameter of `hist` to reveal the
true distribution and compare it with the empirical small-samples
estimates obtainable from 100 samples.
```R
## Set up two-plot layout:
par(mfrow = c(2, 1))

## Generate some interesting mock data
n_samples <- 100
sim_data <- rnorm(n_samples, mean = c(5, 5, 15), sd = c(3, 2, 3))

## Get data min and max for plot alignment
data_range <- range(sim_data)

## 1. Empirical Cumulative Distribution Function:
n <- length(sim_data)
plot(x = sort(sim_data),         # Ordered data plotted on x axis!
     y = seq_len(n_samples)/n_samples,
     type = "S",                 # Plot data as a step-function
     main = "Empirical Cumulative Distribution",
     xlab = "Measured Value (x)", ylab = "Proportion <= x",
     xlim=data_range) 
abline(h = 0, lty = 2)  # Lowest possible proportion
abline(h = 1, lty = 2)  # Highest possible proportion

## 2. Frequency Histogram:
hist(sim_data, col = "lightgrey", breaks = 10,
     main = "Histogram (Binned Density)", xlab = "Measured Value",
     xlim=data_range)

## Reset plotting layout to single panel:
par(mfrow = c(1, 1))
```

Using `sim_data` from above:

1. Use `mean(sim_data <= 12.0)` to calculate the exact proportion of data points that are `<= 12.0`.
2. Verify where this point falls on the vertical axis of your
   empirical distribution function.

---

## 7. The Central Limit Theorem

Now, let's look at distributions not of single observations, but of means of observations.

Below, we will compute means of many samples of the uniform distribution. We also
compute means of random variables given by x^p, where x is uniformly
distributed and p is some fixed number. As you will see, x^p is not
uniformly distributed.

To compute one such mean, we use `mean(runif(sample_size))^p`. In order
to obtain the distribution of these means, we compute 10,000 of them
using `replicate(10000, mean(runif(sample_size)^p)`.

#### Q5. Experimenting with The Central Limit Theorem

Using the code below, experiment with changing the value of `p` (to
change the distribution of the 'measurements' we are averaging over)
and changing the `sample_size` of value x^p that enter out mean. (N.B.:
a `sample_size` of `1` gives you the distribution of x^p itself.)
```R
## Set up two-plot layout:
par(mfrow = c(2, 1))

p <- 1 # try, e.g., 1, 3, 1/2, etc,
sample_size <- 1 # try, 1, 2, 3, ... 10, 100, 1000

## Generate and plot histogram
random_means <- replicate(10000, mean(runif(sample_size)^p))
histo <- hist(random_means, breaks = 50, plot = FALSE)
plot(histo, xlab = "Mean over samples", main = "Zoom")  
plot(histo, xlab = "Mean over samples", main = "Full Range", xlim = c(0,1))  

## Reset plotting layout to single panel:
par(mfrow = c(1, 1))
```
The shape of the distribution you find for large samples sizes is
called the _normal distribution_ or _Gaussian distribution_ or _bell
curve_. The function `rnorm` introduced above generates random numbers with precisely
this distribution. 

#### Q5b. Convergence to the Normal Distribution
Going back to the last script, find out at about what sample size the
distribution of the mean visually becomes a normal
distribution.  Compare this with this with sample sizes we required above to find accurate
estimates of the expectation value of `my_die`.

> So, this is our saviour! While we need many thousands of samples to
> reconstruct the distribution or expectation value of a measurement
> (**making this practically impossible**), we know that the mean over
> a reasonably small number of samples is approximately normally
> distributed. **This observation is the starting point of the
> mathematics underlying most statistical methods used in ecology.**

The 'normal distribution' is actually a family of distributions with
two parameters: the central value, called the _mean of the normal
distribution_, and it's width, quantified by the _standard deviation_.

We already know how to estimate the mean of a distribution. The
function `sd` estimates the standard deviation:
```R
sample_small <- rnorm(10, mean = 0.3, sd = 1.5)
sample_large <- rnorm(10000, mean = 0.3, sd = 1.5)

sd(sample_small)  # Some noisy estiates
sd(sample_large)  # Close to 1.5

## Note the following:
sd(sample_large)^2
sample_mean <- mean(sample_large)
mean( (sample_large - sample_mean)^2 )
var(sample_large)  # Variance 'var()' is the square of the standard deviation

## And so:
sqrt( mean( (sample_large - sample_mean)^2 ) )
sd(sample_large)
```
Because of the approximate relation above, the standard deviation is also called
the _root mean square_ (RMS) _deviation from the mean_.   Make
sure you can identify the 'root', the 'mean' and the 'square' in the last code
block above.


---

## Practical Review Exercises


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

1. Fix the parameters: `n_true = 1000`, `m_marked = 150`, `c_captured = 100`.
2. Using a `for` loop or the `replicate` function, generate a vector
   `pop_estimates` with 500 population estimates using `simulate_recapture_survey`.
4. Calculate the proportion of surveys that failed due to zero recaptures (`mean(is.na(pop_estimates))`).
5. Compute the average population estimate across all valid surveys (`mean(pop_estimates, na.rm = TRUE)`). How close is this to the true value of 1,000?
6. Plot a histogram of the valid estimates and overlay a vertical red
   line (e.g., using `abline`) marking the true population size (`1000`).

---

### Exercise 2 (if you can't get enough): Forest Gap Disturbance & Tree Recruitment Dynamics

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
   initial_stand <- rep(50, 100) # 100 trees, all 50 years old
   
   sim_results <- run_forest_simulation(initial_ages = initial_stand, n_years = 150, p_storm = 0.08)
   
   # Plot mean stand age trajectory over time:
   plot(sim_results$mean_ages, type = "l", col = "forestgreen", lwd = 2,
        xlab = "Time (years)", ylab = "Mean Canopy Age (years)",
        main = "Forest Stand Age")
   ```

Examine the trajectory plot.  What causes the pronounced structure in
the variation of mean age over time?
