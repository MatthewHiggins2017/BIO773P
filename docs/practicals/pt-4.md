# **Practical 4 - Computing in R** 

-------

## Introduction

This session will be a recap of what we saw in practicals 1, 2 and 3. You can also use this session to revisit the parts of the last practicals that are still not entirely clear.
**REMEMBER** The important thing is that you understand what you are doing. It is better to understand well a few exercises than finishing all exercises without entirely knowing what is going on.

## Recap questions

To get back into gear in terms of thinking about programming, we’ll start with a quick recap exercise of the recent material:

#### Q1. What would be the outcome of the code `answer <- rep(x = c(42, 24), times = 42)`? And what would then be the outcome of the code `mean(answer)`?

#### Q2. Use the preloaded R data set 'Indometh' (check `?Indometh` to understand more about the dataset). Subset this data to return only those rows for which the concentration is strictly between 1 and 2. What is the average (mean) concentration for this subset?

#### Q3. Using regular expressions, how would you extract all the words except `antelope` in the vector `c("cameleopard", "eop4a", "kiloparsec", "antelope")`? In R, there are a few ways of getting a result. Find three ways to answer this question.

#### Q4. Write a loop that iterates over the numbers 16 to 49 and prints out the square root of the number each time through (you may have to search around for the square root function). Yes, for this exercise we *absolutely* want you to write a loop to do this, even though there are other ways of getting R to provide the same result.

#### Q5. Make the loop from Intro Q4 store the results to a separate vector called `my_square_roots` instead of just printing the results. What's the value of the 3rd iteration? What is the sum of the square roots of the numbers 16 to 49?



## Open Reading Frames

Protein-coding regions in the genome can be predicted by detecting open reading frames. An open reading frame normally begins with the start codon ‘ATG’ and ends at one of three possible stop codons, ‘TGA’, ‘TAA’ and ‘TAG’. The sequence in between these two points is arranged in 3-base codons.

#### Q6. Write a function which uses regular expressions to detect if a given sequence contains an open reading frame. Test it on the following sequences:

*Hint - an open reading frame should always start with the start codon 'ATG' and end with one of the stop codons. Additionally, the length of the entire sequence should be a multiple of 3. If any of these features are not met, the sequence is not a functional open reading frame.*

```
ATGGATTTTTAG
ATGGATTTTCTAG
CTAATGGATTTTTGAAT
atgctaaactaa
TCGATTAA
```


## Palindromic Sequences

Palindromes are arrangements of words or letters which read the same way whether you read them backwards or forwards, such as the phrase *never odd or even* (if you remove the spaces). In molecular biology, many restriction enzyme sites are palindromic,

#### Q7. Write a function that assesses whether a given word or phrase is a palindrome.
Before starting to code, think about the steps that you would need to go through in order to judge if something is a mirror palindrome.
 * Write these steps as comments in an R script window (or a piece of paper) , then think about how you can tell the computer to execute those steps.
 * Only start writing the code when you have a plan of what you want to do.
 * Write your code as a one-off first - without considering that you'll do a function.
 * Only once you're happy with how things work, sandwich your code into a function format.
 * Use easy test cases where you know the answer in order to check that your function works.

The bottom section of the R help sheets normally have examples of how a command can be used. Sometimes one of these examples will be a way to solve the problem that you are currently working on. The `strsplit` helpsheet is particularly interesting in relation to this question.


## Species names

Run the following line of code to import the `butterfly_sample` and the `butterfly_reference` data frames:

```R
butterfly_sam_url <- "https://raw.githubusercontent.com/MatthewHiggins2017/BIO773P/main/docs/data/butterfly_sample.csv"
butterfly_sample  <- read.csv(file = butterfly_sam_url, header = TRUE)

butterfly_ref_url   <- "https://raw.githubusercontent.com/MatthewHiggins2017/BIO773P/main/docs/data/butterfly_reference.csv"
butterfly_reference <- read.csv(file = butterfly_ref_url, header = TRUE)
```

The `butterfly_reference` data frame contains the species name and the common name of a number of butterflies.

The `butterfly_sample` data frame contains information on butterflies caught in sweep netting surveys in two locations under different pesticide treatments (locations A and B). This data was collected by multiple people, who have recorded the common names of the species that they encountered (without using a standard letter case). In order to be able to compare the diversity between the two different sites, you will need to standardise the names.

#### Q8 Add a new column in the data frame that contains the correct Latin species name for each record in the `butterfly_sample`.

TIP: There are several ways to do this. Remember that R is case sensitive, so you will need to account for case differences in your function. `grep` and `gsub` both allow you to set an `ignore.case = TRUE` option. Alternately, you could use the R commands `toupper()` and `tolower()`. Use the help pages to see how these work, which you can access by typing a question mark before the command - `?toupper`.

#### Q9. From the data above, which location has the greatest number of different species?

#### Q10. Which genus has been caught the greatest number of times?
