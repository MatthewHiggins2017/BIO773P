# R Programming Questions and Answers

## Q1: rep() and mean()

**Question:** What would be the outcome of the code `answer <- rep(x = c(42, 24), times = 42)`? And what would then be the outcome of the code `mean(answer)`?

**Answer:**

The `rep()` function repeats the vector `c(42, 24)` a total of 42 times.

```r
answer <- rep(x = c(42, 24), times = 42)
# Result: a vector of length 84 containing: 42, 24, 42, 24, 42, 24, ... (repeated 42 times)
```

The outcome is a vector with 84 elements (2 elements × 42 repetitions), alternating between 42 and 24.

```r
mean(answer)
# Result: 33
```

The mean is **33** because:
- There are 42 instances of 42 and 42 instances of 24
- Mean = (42 × 42 + 24 × 42) / 84 = (1764 + 1008) / 84 = 2772 / 84 = 33
- Or simply: (42 + 24) / 2 = 33

---

## Q2: Subsetting Indometh Dataset

**Question:** Use the preloaded R data set 'Indometh' (check `?Indometh` to understand more about the dataset). Subset this data to return only those rows for which the concentration is strictly between 1 and 2. What is the average (mean) concentration for this subset?

**Answer:**

```r
# Load the dataset
data(Indometh)

# Subset rows where concentration is strictly between 1 and 2
subset_data <- Indometh[Indometh$conc > 1 & Indometh$conc < 2, ]

# Calculate the mean concentration
mean_concentration <- mean(subset_data$conc)
mean_concentration
# Result: 1.355
```


---

## Q3: Regular Expressions - Extracting Words

**Question:** Using regular expressions, how would you extract all the words except "antelope" in the vector `c("cameleopard", "eop4a", "kiloparsec", "antelope")`? Find three ways to answer this question.

**Answer:**

```r
words <- c("cameleopard", "eop4a", "kiloparsec", "antelope")
```

### Method 1: Using `grep()` with `value = TRUE` and `invert = TRUE`
```r
# Extract all words that don't match "antelope"
result1 <- grep("^antelope$", words, value = TRUE, invert = TRUE)
result1
# Result: "cameleopard" "eop4a" "kiloparsec"
```

### Method 2: Using `grepl()` with logical negation
```r
# Create a logical vector and subset
result2 <- words[!grepl("^antelope$", words)]
result2
# Result: "cameleopard" "eop4a" "kiloparsec"
```

### Method 3: Using `grep()` to get indices and negative indexing
```r
# Find the index of "antelope" and exclude it
antelope_index <- grep("^antelope$", words)
result3 <- words[-antelope_index]
result3
# Result: "cameleopard" "eop4a" "kiloparsec"
```

---

## Q4: Loop to Print Square Roots

**Question:** Write a loop that iterates over the numbers 16 to 49 and prints out the square root of the number each time through.

**Answer:**

```r
# Loop through numbers 16 to 49 and print square roots
for (i in 16:49) {
  print(sqrt(i))
}
```

**Output:**
```
[1] 4
[1] 4.123106
[1] 4.242641
[1] 4.358899
[1] 4.472136
[1] 4.582576
[1] 4.690416
[1] 4.795832
[1] 4.898979
[1] 5
[1] 5.09902
[1] 5.196152
[1] 5.291503
[1] 5.385165
[1] 5.477226
[1] 5.567764
[1] 5.656854
[1] 5.744563
[1] 5.830952
[1] 5.916079
[1] 6
[1] 6.082763
[1] 6.164414
[1] 6.244998
[1] 6.324555
[1] 6.403124
[1] 6.480741
[1] 6.557439
[1] 6.633249
[1] 6.708204
[1] 6.782330
[1] 6.855655
[1] 6.928203
[1] 7
```

---

## Q5: Storing Loop Results in a Vector

**Question:** Make the loop from Intro Q4 store the results to a separate vector called `my_square_roots` instead of just printing the results. What's the value of the 3rd iteration? What is the sum of the square roots of the numbers 16 to 49?

**Answer:**

```r
# Initialize an empty vector
my_square_roots <- c()

# Loop through numbers 16 to 49 and store square roots
for (i in 16:49) {
  my_square_roots <- append(my_square_roots, sqrt(i))
}

```

**Answers to specific questions:**

1. **Value of the 3rd iteration:**
```r
my_square_roots[3]
# Result: 4.242641
```
The 3rd iteration corresponds to the square root of 18, which is approximately **4.242641** (or exactly √18 = 3√2).

2. **Sum of all square roots:**
```r
sum(my_square_roots)
# Result: 191.4955
```

**Verification:**
```r
length(my_square_roots)  # Should be 34
my_square_roots[1]        # Should be 4 (sqrt of 16)
my_square_roots[34]       # Should be 7 (sqrt of 49)
```

---

## Q6: Open Reading Frame Detection

**Question:** Write a function which uses regular expressions to detect if a given sequence contains an open reading frame. An open reading frame normally begins with the start codon 'ATG' and ends at one of three possible stop codons, 'TGA', 'TAA' and 'TAG'. The sequence in between these two points is arranged in 3-base codons.

Test it on the following sequences:
- ATGGATTTTTAG
- ATGGATTTTCTAG
- CTAATGGATTTTTGAAT
- atgctaaactaa
- TCGATTAA

**Hint:** An open reading frame should always start with the start codon 'ATG' and end with one of the stop codons. Additionally, the length of the entire sequence should be a multiple of 3.

**Answer:**

```r
# Function to detect open reading frame
is_open_reading_frame <- function(sequence) {
  # Convert to uppercase for consistency
  sequence <- toupper(sequence)
  
  # Check if sequence starts with ATG and ends with stop codon (TGA, TAA, or TAG)
  # Check if total length is multiple of 3
  # Pattern: ^ATG - starts with ATG
  #          (.{3})* - followed by zero or more triplets of any characters
  #          (TGA|TAA|TAG)$ - ends with one of the stop codons
  
  pattern <- "^ATG(.{3})*(TGA|TAA|TAG)$"
  
  # Test the pattern
  result <- grepl(pattern, sequence)
  
  return(result)
}

# Test on the provided sequences
test_sequences <- c(
  "ATGGATTTTTAG",
  "ATGGATTTTCTAG",
  "CTAATGGATTTTTGAAT",
  "atgctaaactaa",
  "TCGATTAA"
)

# Test each sequence
for (seq in test_sequences) {
  result <- is_open_reading_frame(seq)
  status <- ifelse(result, "Valid ORF", "NOT a valid ORF")
  print(paste(seq, "->", status))
}
```

**Output:**
```
ATGGATTTTTAG         -> Valid ORF
ATGGATTTTCTAG        -> NOT a valid ORF
CTAATGGATTTTTGAAT    -> NOT a valid ORF
atgctaaactaa         -> Valid ORF
TCGATTAA             -> NOT a valid ORF
```


---

## Q7: Palindromic Sequences

**Question:** Write a function that assesses whether a given word or phrase is a palindrome. Palindromes are arrangements of words or letters which read the same way whether you read them backwards or forwards, such as the phrase "never odd or even" (if you remove the spaces). In molecular biology, many restriction enzyme sites are palindromic.

**Hint:** Before starting to code, think about the steps needed to judge if something is a mirror palindrome. Write these steps as comments first, then implement them. Use easy test cases where you know the answer.

**Answer:**

### Step 1: Planning (as comments)

```r
# Steps to check if a sequence is a palindrome:
# 1. Convert the input to uppercase (for consistency)
# 2. Remove spaces and special characters (for phrase palindromes)
# 3. Split the string into individual characters
# 4. Reverse the order of characters
# 5. Compare the original with the reversed version
# 6. Return TRUE if they match, FALSE otherwise
```

### Step 2: One-off code (testing the logic)

```r
# Test with a simple case
test_word <- "GAATTC"  # EcoRI restriction site - palindromic

# Convert to uppercase
test_word <- toupper(test_word)

# Remove spaces (not needed for this case, but good practice)
test_word <- gsub(" ", "", test_word)

# Split into individual characters
chars <- strsplit(test_word, "")[[1]]
chars
# Result: "G" "A" "A" "T" "T" "C"

# Reverse the characters
reversed_chars <- rev(chars)
reversed_chars
# Result: "C" "T" "T" "A" "A" "G"

# Join back into a string
reversed_word <- paste(reversed_chars, collapse = "")
reversed_word
# Result: "CTTAAG"

# Compare
test_word == reversed_word
# Result: FALSE (GAATTC is not the same as CTTAAG)

# Let's test with a true palindrome
test_word2 <- "GAATTC"
# Actually, for DNA palindrome, we need reverse complement!
# But for simple text palindrome, let's use:
test_word2 <- "racecar"
test_word2 <- toupper(test_word2)
chars2 <- strsplit(test_word2, "")[[1]]
reversed2 <- paste(rev(chars2), collapse = "")
test_word2 == reversed2
# Result: TRUE
```

### Step 3: Create the final Function

```r
# Function to check if a word or phrase is a palindrome
is_palindrome <- function(text) {
  # Convert to uppercase for case-insensitive comparison
  text <- toupper(text)
  
  # Remove spaces and non-alphanumeric characters
  text <- gsub("[^A-Z0-9]", "", text)
  
  # Split into individual characters
  chars <- strsplit(text, "")[[1]]
  
  # Reverse the characters
  reversed_chars <- rev(chars)
  
  # Join back into a string
  reversed_text <- paste(reversed_chars, collapse = "")
  
  # Compare original with reversed
  is_same <- (text == reversed_text)
  
  return(is_same)
}

# Test the function
test_cases <- c(
  "racecar",
  "hello",
  "A man a plan a canal Panama",
  "never odd or even",
  "GAATTC",
  "palindrome",
  "madam",
  "step on no pets"
)

# Test each case
for (test in test_cases) {
  result <- is_palindrome(test)
  print(paste(test, "->", ifelse(result, "IS a palindrome", "NOT a palindrome")))
}
```

**Output:**
```
[1] "racecar -> IS a palindrome"
[1] "hello -> NOT a palindrome"
[1] "A man a plan a canal Panama -> IS a palindrome"
[1] "never odd or even -> IS a palindrome"
[1] "GAATTC -> NOT a palindrome"
[1] "palindrome -> NOT a palindrome"
[1] "madam -> IS a palindrome"
[1] "step on no pets -> IS a palindrome"
```

## Q8: Species names

```r

butterfly_sam_url <- "https://raw.githubusercontent.com/MatthewHiggins2017/BIO773P/main/docs/data/butterfly_sample.csv"
butterfly_sample  <- read.csv(file = butterfly_sam_url, header = TRUE)

butterfly_ref_url   <- "https://raw.githubusercontent.com/MatthewHiggins2017/BIO773P/main/docs/data/butterfly_reference.csv"
butterfly_reference <- read.csv(file = butterfly_ref_url, header = TRUE)

# Update both names to uppercase() and replace any space with hyphen 
butterfly_sample$Species <- toupper(butterfly_sample$Species)
butterfly_sample$Species <- gsub(" ", "-", butterfly_sample$Species)


butterfly_reference$Common.name <- toupper(butterfly_reference$Common.name)
butterfly_reference$Common.name <- gsub(" ", "-", butterfly_reference$Common.name)

# Perform a dataframe merge to add the latin name to the sample column 
butterfly_sample <- merge(
  butterfly_sample,
  butterfly_reference[, c("Common.name", "Latin.name")],
  by.x = "Species",
  by.y = "Common.name",
  all.x = TRUE
)

```


## Q9: Location with greatest number of species

```

# Loop over each location
for (location_code in c("A","B")) {
  
  # Subset the dataframe 
  sub <- subset(butterfly_sample, butterfly_sample$Location==location_code)
  
  # Determine the number of unique species per location
  number_of_unique <- length(unique(sub$Latin.name))
  
  print(paste("Location:",location_code,"Unique Species:",number_of_unique))
  
}

```