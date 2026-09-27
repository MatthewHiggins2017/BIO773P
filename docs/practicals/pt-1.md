
# **Practical 1 - Introduction to RStudio and basic data structures**

----------------

## Introduction

This practical series is aimed at teaching basic programming in R. We
will not be covering stats in these practicals. However, do not
relax too much, as these practicals will move fairly quickly, and you
will need to stay focused in order to do well in the final exam. Make
use of R’s help system, your neighbour, and if you're really stuck
don't be afraid to ask a demonstrator.

-----------------------

## The RStudio environment

Open RStudio following the instructions [here](../Cluster_Access.md).

RStudio is a _frontend_ to R. It allows you to easily use R, while R
itself is the engine working in the background to do all the
calculations.  Hence, we'll often say "R does this" or "R does that"
while we are working with RStudio.

Now, please acquaint yourself with RStudio. 

### The Console

  * In the **bottom left** there's a tab called "Console". You can click
there and do some simple maths.
Try typing `1 + 2` in this window and pressing Enter (or Return).  As output you'll see
```
[1] 3
```
The cryptic `1` in `[1]` indicates that the line starts with the *1st* element of
vector of numbers (which here has only a single element). The `3` is
the result of the calculation that you just asked R to do.

  * Continuing on the console, type `a <- 7 - 2` and then Enter.  Now the
answer is not printed out.  However, if you look in "Environment" tab
in the **top right**, you'll see that it now displays a line with `a` and
the assigned value (the result of `7 - 2`), that is, `5`.

  * You can recover the value of `a` by typing `a` in the console and
    then Enter.  R evaluate this line just as the previous maths
    problem `1 + 2`.  It knows that the value assigned to `a` is 5,
    and so outputs this value.
	
  * In the console, you can recover lines that you have previously
    entered by pressing the [UP] arrow on your keyboard. Give it a
    try.
	
	
If you do a bit more work in R, remembering and recovering what you
did in the console becomes impossible. As scientists we want our work
to be *reproducible*.  Hence, please *stop typing in the console*. Use
it only for small calculations that you won't need again.

### The R Script Editor 

Instead, we will write all the commands we use into a file, an "R
script", and execute them from there.

  * In the pull-down menu on the **top left**, click on File -> New File
    -> R Script.  A new, blank window appears.
	
  * Type in a new maths problem, e.g., `2 * a + 8`. Press enter.
    Nothing happens. We haven't told R, yet, to evaluate the problem. 
	
  * Now place your cursor back into the line with this maths problem
    and press [Control]-Enter. You'll see that the line is sent to the
    console below and evaluated.
	
  * In your R script, type a new command, e.g., `a <- a + 1000`, and
    then [Control]-Enter (sometimes, it's [Shift] rather than
    [Control], on a Mac it's different again).  Observe what
    happens. Observed also how the value of `a` changed in the
    top-right Environment tab.
	
  * You can also run entire scripts at once. For this, press
    [Control]-A (to highlight the full script), then [Control]-Enter,
    and observe what happens. This is very useful if you found an
    error in one step of a complicated calculation and want to re-do
    the entire calculation after correcting the error.
    
  * You can also run parts of your script repeatedly.  For example,
    highlight `a <- a + 1000` with your mouse, and then press
    [Control]-Enter repeatedly and see how the value of `a` change in
    the Environment tab.
	
  * To save a script, go back to the pull-down menu on the top left
    and click on File -> Save. Choose a meaningful name for your
    script. The ending ".R" will automatically be appended to the
    filename.
	
### The Help Tab

The most important tab in the **bottom right** is the Help tab. For
example:

  * In your R Script, type `sum` and move the cursor so it is on top
    of `sum`. Then press `F1` on your keyboard. An explanation of the
    `sum` function appears in the Help tab in the bottom
    right. (Alternatively, you can execute the line `?sum`.)
	
  * Help pages are very useful *if you read them carefully*.  To
    start, I recommend reading first the "Description" and "Arguments"
    section of a help page, and then skipping down to the "Value"
    section and finally to "Examples". Give it a try.
	
  * Can you understand what `sum` does? When you run the commands in
    the Example section of the help page, does the output make sense
    to you?
	
  * Compute the sum of all integers from 1 to 100.
  
  * **Often, it's rather easy to guess what a function does from
    seeing a use case. We will therefore occasionally introduce new
    functions in practicals without much explanation. Please consult the help page
    for details. If you remain confused, please ask a demonstrator.**
	

-------------------

## Variables
A variable is a symbolic way of storing a particular set of values
and/or characters. We have seen the use of the variable `a`
before. Now, execute the lines

```R
x <- 5
y <- 225
```

This assigns the number `5` to the symbol `x`. You can run operations
on variables (e.g. `3*x^2`), create new variables using existing
variables (e.g. `y <- 3*x^2`), and reassign existing variables to a
new value (e.g.` x <- 3*x^2`; try running that a few times).  Please
always use `<-` for assignment.

Variable names should start with a letter and can contain letters,
numbers, "\_" and ".". However, using "." is discouraged.  Use "\_" to
separate words.

It is good practice to use using descriptive names (avoiding `x` and
`y`).  Try to make your code easily understandable for other people,
including your future self.  Descriptive variable names can work magic
for this. An exception are variables that occur very frequently in a short
passage of your script, where brevity can improve readability.

#### Q1. Create a new variable, `z`, out of the values in `x` and `y`. You can choose any formula you want for `z`, as long as it contains both `x` and `y`.


-------------------------

## Data classes
There are several classes (or types) of data.  The class of your data tells you whether R interprets the data as numbers, letters, factors (i.e. categories), logical values or a number of alternatives. You can use the function `class()` to check the class of a variable.

You may have seen numeric (`x <- 5`) and character variables (`x <-
"hello"`).  Numeric values are internally represented as
double-precision real values.  You can force representation as an
*integer* and dropping of the fractional part using, e.g.,
`as.integer(5.3)` or `as.integer(2)`.

An important class of data that you might not be familiar with is
**logical data**. Logical data can only take one of two possible
values: `TRUE` or `FALSE` (abbreviated `T` and `F`). There are a
number of different ways of arriving at a logical variable. The most
obvious is to simply define a variable as true, for example:

```R
x <- TRUE
```

or false, for example

```R
x <- FALSE
```

Try creating a logical variable in this way and look at the class of the variable - if you have done it correctly it should read "logical" (keep in mind that R is case-sensitive, i.e. `x <- false` will throw an error).

However, this is not the way that logical variables tend to be used in programming. We often create logical variables through a particular type of calculation, called a **logical expression**. You can think of a logical expression as a statement (or a question) that we send to R, which may be a true statement, or it may be a lie! For example, try evaluating the code

```R
5 > 4
```

This statement is clearly true and accordingly R returns a value `TRUE`. The statement

```R
5 < 4
```
on the other hand, returns the value `FALSE`. We can assign this logical value to a variable by using the logical expression as input to the variable. This sounds complicated, but in practice it is very simple. Take the example

```R
x <- (5 > 4)
```

You can read the expression above as "the variable x is assigned the outcome of the logical expression 5 > 4". In this particular example the variable `x` will get the value `TRUE`. Notice that the logical expression itself is within parentheses. This is not strictly required, but is good coding practice as it avoids confusion between the assignment symbol and the logical expression.

The main logical operators that you should be familiar with are the following:

* `>` is greater than
* `<` is less than
* `>=` is greater than or equal to
* `<=` is less than or equal to
* `==` is equal to
* `!=` is not equal to

Play around with some of these operators in your own made-up logical expressions. Make sure you are comfortable assigning a logical value to a variable.

We can create more sophisticated logical expressions using the "and" command and the "or" command. The "and" command is written `&` and called ampersand (keyboard shortcut Shift+7), while the "or" command is written `|` and called a "pipe" or vertical bar (keyboard shortcut Shift+\ on a standard Windows keyboard). These "operators" can be placed between two or more logical expressions - exactly as you would do in a spoken sentence. For example, the expression

```R
(x > 5) & (x <= 10)
```
can be read "x is greater than five, and x is less than or equal to ten". Similarly, the expression

```R
(x < 6) | (x == 12)
```

can be read "x is less than six, or x is equal to twelve". By using a combination of these operators, while making good use of parentheses, it is possible to come up with some quite complex statements.


**A short note on classes:**

It's easy to get numeric and character data confused. For example, in `x <- "blue"`, `x` is of class "character", while in `x <- 4`, `x` is of class "numeric". The problem comes if you type:

```R
x <- "4"
```

In this case `x` is of class "character" because of the quotation marks. Try typing `class(x)` if you are confused. You can also type `is.numeric(x)` and `is.character(x)`.



#### Q2. Let us define the variable `x <- 10`. What will the outcome of the following expressions be?

* `(x > 5)`
* `(x <= 10)`
* `(x != 10)`
* `(x < 5) & (x < 105)`
* `(x < 4) | (x > 14) | (x == 10)`

#### Q3. Below are some of the rules that logical operators follow. Write in plain English what it means.

* `TRUE & TRUE` is `TRUE`
* `TRUE & FALSE` is `FALSE`
* `FALSE & TRUE` is `FALSE`
* `TRUE | FALSE` is `TRUE`
* `FALSE | TRUE` is `TRUE`

#### Q4. Given three variables (`x`, `y` and `z`), how would you write the following sentence in a logical expression?

*The variable `x` is less than variable `y`, or greater than variable `z`?*

-------------------------------

## Types of Objects
### Scalars and vectors
Objects can be of different types. So far, we have looked at **scalar
objects**, which contain a single variable (e.g. `x <- 3`). Another
very common type of object is the **vector**, which is an object
containing several scalars of the same class. Please assign the
following vectors to variables for later use:

```R
numeric_vec   <- c(1, 1, 2, 3, 5, 8)   # c is a function for combining values into a vector or list.
seq_vec1      <- seq(from = 10, to = 5) 
seq_vec2      <- seq(from = 0, to = 10, by = 0.25)
rep_vec       <- rep(x = 2, times = 12)
character_vec <- c("How", "Now", "Brown", "Cow")
logical_vec   <- c(TRUE, TRUE, FALSE, TRUE, TRUE, FALSE)
```
In the Environment tab you can see the vectors assigned to the variables.

N.B.: Confusingly, R does technically not distinguish between scalars
and vectors!  Scalars are simply length-one vectors.  If you query the
class of a vector, e.g., `class(c(TRUE, FALSE, FALSE))`, this will
simply return the class of its elements (here "logical") rather than
something like "vector of logical". This strange convention allows R
to operate very efficiently on vectors.

Many operations that work for scalars you can also perform on entire
vectors. In many cases, they are done element-by-element (e.g. `vec1 *
3` or `vec1 * vec2` - *make sure you understand what R is doing
here*).

You can access individual elements of a vector by using a scalar object
as an index (e.g. `x[3]`).  You can also use vectors to
access a set of elements (e.g. `x[seq(from = 1, to = 5)]`) or specific
elements (e.g. `x[c(2, 7, 9)]`) of a vector or variable. Remember
that, for some calculations between different vectors, the vectors
need to be compatible. This generally means they have lengths that are
multiple of each other. Note that in R, the first position of a vector
has the index 1, unlike in some other programming languages where the
first position has the index 0.

#### Q5. What number would you obtain if you evaluate `seq_vec2[3]`? (try working this out for yourself before typing it into R)
 
#### Q6. What happens when you evaluate `seq_vec2[seq(from = 1, to = 5)]`?

#### Q7. How would you retrieve just the 8th, 13th and 21st elements of `seq_vec2`?

Simple calculations can be performed on vectors, in which case the operation is applied to every element of the vector separately. For example, try evaluating

```R
numeric_vec_squared <- numeric_vec ^ 2
```

You will find that `numeric_vec_squared` contains values taken from `numeric_vec`, where each element has been squared individually. Similarly, you can create logical expressions that apply to the whole matrix, such as `numeric_vec > 3`

You can also perform operations involving several vectors, as long as the vectors have compatible lengths. For example, try typing

```R
combined_vec1 <- numeric_vec * seq_vec1
```

You will find that each of the elements of `combined_vec1` is equal to the product of the corresponding elements in `numeric_vec` and `seq_vec1`

#### Q8. Try evaluating `combined_vec2 <- numeric_vec * seq_vec2`. What happens? Why?

#### Q9. Try using the `length` function to find the lengths of the two vectors we tried to multiply.

#### Q10. In fact, compatible does not mean that vectors have to be exactly the same length. Try evaluating `combined_vec3 <- numeric_vec * rep_vec` and look at the result. What is the logic behind the values produced?

### Matrices
Another major type of object in R is the **matrix**. A matrix is
simply a rectangular grid of values, with *rows* and *columns*.  One
of the simplest ways of producing a matrix is by combining several
vectors through the functions `rbind()` and `cbind()`. Try
`rbind(numeric_vec, seq_vec2)`.

#### Q11. What happens when you evaluate `rbind(numeric_vec, seq_vec2)`? What happens when you evaluate `cbind(numeric_vec, seq_vec2)`? Why?
#### Q12. When you evaluate `rbind(numeric_vec, seq_vec2)` you get a warning message. Why?
#### Q13. What happens when you evaluate `rbind(numeric_vec, rep_vec)`? Why?

You can also create matrices directly in a number of different ways.
For each of the matrices below, understand how the command and its
arguments determine the matrix that is produced.

```R
matrix1 <- matrix(data = seq_len(length.out = 24), nrow = 6, ncol = 4)
matrix2 <- matrix(data = seq_len(length.out = 24), nrow = 6, ncol = 4, byrow = TRUE)
matrix3 <- diag(x = 5, nrow = 3, ncol = 2)
matrix5 <- matrix(data = "Hello World", nrow = 2, ncol = 5)
```

As with vectors, you can access the elements of a matrix using square
brackets.  But now you need two indices, the fi**R**st for the
**R**ow, the s**C**ond for the **C**olumn. Try:

 * `matrix1[4, 3]`
 * `matrix1[seq_len(length.out = 2), seq_len(length.out = 2)]`
 
As a general R convention, *a blank instead of indices means that the
full range of indices*, for example the entire row or column.  Try
this:
 
 * `matrix1[seq_len(length.out = 2), ]`

#### Q14. How do you retrieve the 2nd column of `matrix2`?

You can perform simple calculations on matrices, in which case the calculation applies to each element separately:

```R
(matrix3 + 2) * 2
```
You can also combine the values in several matrices, as long as the dimensions of the matrices are compatible

```R
(matrix1 * 100) + matrix2
```

Finally, you can create logical expressions that apply to an entire matrix. For example, try evaluating:

```R
(matrix1 > 10)
```

There are a number of useful functions that can be applied to matrices. Have a look at each of the following functions, and try to make sense of the output:

* `length()`
* `dim()`
* `t()`
* `colSums()`
* `summary()`

Keep in mind that if you ever need help in understanding a function, just bring up the help page for that function.

#### Q15. The variable `matrix1` describes a matrix produced by the following code:

```R
matrix1 <- matrix(data = seq_len(length.out = 50), nrow = 10, ncol = 5)
```

What number would we expect to see when we evaluate `matrix1[1, 2]`? Try to answer this without evaluating the code! Why not a different number?

#### Q16. Which of these commands would output the 2nd and 4th columns of matrix1 only (again, try answering this without evaluating the code!)?

```R
matrix1[2, 4]
matrix1(, (2, 4))
matrix1[c(2, 4), ]
matrix1[, c(2, 4)]
```

#### Q17. Write your own script (or lines in your R script) for creating a matrix from three separate vectors. The first vector, `vec1`, should be 50 elements long, and should simply contain the numbers 1 to 50. The second vector, `vec2`, should contain the square of these numbers (i.e. `vec1` raised to the power 2). The third vector, `vec3`, should contain the cube of these numbers (i.e. `vec1` raised to the power 3). Finally, create a matrix, `my_matrix`, which has `vec1` as the first row, `vec2` as the second row, and `vec3` as the third row.

### Data frames
A very common type of object is the **data frame**. On the face of it,
these look very similar to matrices. However, there are some important
differences between data frames and matrices. The most important is
that, in a matrix, all the elements need to be of the same class,
while in a data frame different columns can be from different
classes. Several data frames are loaded into R by default (e.g.,
`Puromycin`). Check the help to understand what this data includes:
`?Puromycin`.

R allows you to get to specific columns using their name and the
dollar sign: `Puromycin$rate`.  Alternatively, you provide the name of
a column in quotation marks as if it were an index:
`Puromycin[,"rate"]`. (It is also possible to use indices like in
matrices, for example`Puromycin[1, 1]` or `Puromycin[, 2]`. But using
indices can make code difficult to read and less robust to changes in
data structure.)

Note that, although the data frame is of class data frame, typing
`Puromycin$rate` will return a vector of class numeric (try using the
function `class()` to check this).

The column `state` of `Puromycin` is of class `factor`.  Factors are
used to represent different categories of things that are in no
particular order.  Each category is called a `level`.  A `level` has
both an integer and character value (the name of the `level`)
associated with it.  Usually, levels are numbered in alphabetic
order. You can convert back and forth between the representations.
Try:

```R
Puromycin$state
levels(Puromycin$state)
as.integer(Puromycin$state)
as.character(Puromycin$state)
as.factor(as.character(Puromycin$state))
```

### Lists
The last data type that is commonly seen in R is the **list**. A list
is a bit like a complicated vector, where the elements can be objects
of any type, and shape. You can assign names to list element using
`=`.  For example, we can make a list of vectors like this:

```R
my_list <- list(a = c(1, 2, 3), b = c(5, 6, 7, 8, 9, 10), d = c('G', 'H'))
```

We can access elements from the list using their index, with brackets
`my_list[[1]]`, *but you should NOT do this!*. Instead, use the names
whenever possible, either using the bracket notation
(`my_list[['a']]`) or the dollar sign (`my_list$a`). Lists can get
very complex, since there is no limits on the data type of the
elements. Therefore, you can get lists of vectors, lists of lists,
lists of vectors and lists, etc…

Lists are often used in R to *package* different kinds of data that
logically belong together. For example

```R
david <- list(name = "David Attenborough", born = as.Date("1926/05/08"),
              died = as.Date(NA),
              sisters = character(0),
              brothers = c("Richard", "John"),
              parents = list(mother = "Mary", father = "Frederick")
              )
```


In the Environment tab, click on the little arrow to reveal the structure
of `david`.

#### Q18. Create a list that combines different kinds of data about a hypothetical ecosystem.

There are other types of data in R, with many being specific to
particular libraries.  Often these more complex data are constructed
as lists with named elements.


--------------------------------------

##  Accessing data frames
We have already come across one way of subsetting through the use of square brackets. By typing, for example,

```R
Puromycin
Puromycin[c(1, 2, 3), ]  # this is acceptable
Puromycin[, 2]           # not recommended: use the column's name instead
```
we can isolate certain rows of the data frame that we are interested
in. Using numbers to access specific rows is ok, but we should not
do this with columns. Instead, we can use the
dollar sign `$` or to get a specific column, or replace a column index
by a column name:

```R
Puromycin$rate
Puromycin[ , "rate"]
```

We can isolate data easily by using the logical statements mentioned
above. For example, we can check which rows have a rate that is less
than 100:

```R
# returns TRUE or FALSE
Puromycin$rate < 100
# returns which elements are TRUE
which(Puromycin$rate < 100)
```

We can use the subset function to select the rows for which the statement is TRUE:

```R
# returns all the columns, but only the rows for which
# Puromycin$rate < 100 is TRUE
puromycin_sub <- subset(Puromycin, rate < 100)
```

We could subset by the "state" column:

```R
puromycin_treated <- subset(Puromycin, subset = state == "treated")
```

This will return all fields for which the state is equal to
"treated". 

N.B.: Notice that, even though `state` is a factor, we can select a
particular level by comparing with a corresponding character value
without conversion. This works because R often automatically converts
between different classes of data to make sense of it.  Here, this is
convenient.  However, in other situations is can mask unintended use
of inappropriate classes of data and lead to errors or unexpected
results in the course of a longer calculation. It is good practice to
check classes and convert to the most appropriate one using
`as.numeric()`, `as.factor()`, `as.character()`, etc.

#### Q19. How would you subset the variable `Puromycin` to return only those fields for which the concentration is greater than 0.1?
#### Q20. What is the average (mean) concentration for these cells?
#### Q21. How would you subset the variable `Puromycin` to return only the fields for which the cells were not treated?
#### Q22. How would you subset the variable `Puromycin` to return only the fields for which the concentration is less than 0.5 and the rate is greater than 100 (remember the "and" command from logical expressions)?
#### Q23. How would you subset the variable `Puromycin` to return only the fields for which the concentration is greater than 0.2 and the cells have been treated?
#### Q24. How would you subset the variable `Puromycin` to return only the fields for which the concentration is less than 0.1 or greater than 0.2?
#### Q25. How would you subset the variable `Puromycin` to return only the fields for which the concentration is less than 0.2 and the rate is less than 70 and the cells have been treated?


