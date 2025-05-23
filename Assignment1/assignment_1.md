ABY-Statistic-Assignment-1
================

# 1:

\*Some film review aggregator websites publish ranked lists of movies
based on the number of positive critical reviews out of a total number
counted for each movie. Sometimes an “adjusted score” is used, for which
a movie with a higher approval percentage can actuallyrank lower on the
list.

Consider the following hypothetical scenario: Movie 1: 9 positive
reviews out of 10 (90%) Movie 2: 425 positive reviews out of 500 (85%)
Assume that reviews of Movie i have a common probability pi of being
positive (dependingon the movie) and are independent (conditional on
pi). Assume a U(0, 1) prior on each pi.\*

## 1a

*Determine the posterior distribution of p1 and of p2 (separately).
(Name the type of distribution and give the values of its defining
constants.)*

**Solution** - Based on the desc above and since there are positive and
negative review, for both movies, we will have **binomial likelihood** -
Because the desc assumed a U(0,1) equivilant to uniform distribution on
interval \[0,1\] therefore we could assume both movies have **uniform
prior**

This then lead into **Beta posterior** based on conjugate prior

Movie 1: - prior: Beta (1,1) - positive: 9 - Total review: 10 - negative
= 10 - 9 = 1

**Postetior** ~ Beta(prior alpha + positive, prior beta + negative) =
Beta(1+9,1+1) **Movie1_Postetior** ~ Beta(10,2)

Movie 2: - prior: Beta (1,1) - positive: 425 - Total review: 500 -
negative = 500 - 425 = 75

**Postetior** ~ Beta(prior alpha + positive, prior beta + negative) =
Beta(1+425,1+75) **Movie1_Postetior** ~ Beta(426,76)

## 1b

*Which movie ranks higher according to posterior mean? According to
posterior median? According to posterior mode? Show your computations.
(For median, use R function qbeta. For mean and mode, use formulas in
BDA3, Table A.1. Do not use simulation, as it may not be sufficiently
accurate.*

``` r
find_higher <- function(value1, value2, name1 = "Value 1", name2 = "Value 2") {
    cat(sprintf("%s: %.4f\n", name1, value1))
    cat(sprintf("%s: %.4f\n", name2, value2))
    
    if (value1 > value2) {
        cat(sprintf("\n%s is higher by %.4f\n", name1, value1 - value2))
        return(value1)
    } else if (value2 > value1) {
        cat(sprintf("\n%s is higher by %.4f\n", name2, value2 - value1))
        return(value2)
    } else {
        cat("\nValues are equal\n")
        return(value1)
    }
}
```

Posterior Mean: $$
\text{Posterior Mean} = \frac{\alpha}{\alpha + \beta}
$$

``` r
# Utiliy fuction for Posterior Mean
post_mean <- function(alpha,beta){
    mean = alpha/(alpha + beta)
    return (mean)
}

# Movie 1 Posterior Mean ~ 83.33%
movie1_mean = post_mean(10,2)

# Movie 2 Posterior Mean ~ 84.86%
movie2_mean = post_mean(426,76) 

# Output
result <- find_higher(post_mean(10,2),post_mean(426,76),"Post_mean_movie_1","Post_mean_movie_1")
```

    ## Post_mean_movie_1: 0.8333
    ## Post_mean_movie_1: 0.8486
    ## 
    ## Post_mean_movie_1 is higher by 0.0153

Posterior Mod $$
\text{Posterior Mod} = \frac{\alpha - 1}{\alpha + \beta - 2}
$$

``` r
# Utiliy fuction for Posterior Mod
post_mod <- function(alpha,beta){
    mod = (alpha - 1)/(alpha + beta - 2)
    return (mod)
}

# Output
result <- find_higher(post_mod(10,2),post_mod(426,76),"Post_mod_movie_1","Post_mod_movie_1")
```

    ## Post_mod_movie_1: 0.9000
    ## Post_mod_movie_1: 0.8500
    ## 
    ## Post_mod_movie_1 is higher by 0.0500

Posterior Median

``` r
median1 <- qbeta(0.5, 10, 2)
median2 <- qbeta(0.5, 426, 76)

# Output
result <- find_higher(median1,median2,"Post_median_movie_1","Post_median_movie_2")
```

    ## Post_median_movie_1: 0.8520
    ## Post_median_movie_2: 0.8491
    ## 
    ## Post_median_movie_1 is higher by 0.0030

# 2

*File randomwikipedia.txt contains the ID number and number of bytes in
length for 20 randomly selected English Wikipedia*

``` r
data <- read.table("randomwikipedia.txt", header=TRUE)
head(data)
```

    ##     pageID bytes
    ## 1  3952653 18284
    ## 2 22091611  2222
    ## 3 72611703  4037
    ## 4  1995807  7533
    ## 5 64302262   984
    ## 6 12066155 10881

## 2a.

*Diplay a histogram of article length and describe the distribution*

``` r
# Display histogram
hist(data$bytes, 
     main = "(1) Histogram of Wikipedia Article Lengths",
     xlab = "Article Length (bytes)",
     ylab = "Frequency")
```

![](assignment_1_files/figure-gfm/-%20histogram%20(1)-1.png)<!-- -->

Looking at this historam We can see the shape appears to be very heavy
right-skew

*Transform article length to the (natural) log scale. Then re-display
the histogram and describe the distribution.*

``` r
hist(log(data$bytes),
     main = "(2) Log-Transformed - Histogram of Article Lengths",
     xlab = "Log Article Length (log bytes)",
     ylab = "Frequency")
```

![](assignment_1_files/figure-gfm/-%20histogram(2)-1.png)<!-- -->

After the log transformation, the distribution’s right-skew is
significantly reduced, provide more symmetric (normal-like)
distribution.

## 2b.

*Let yi be length of article i on the log scale: the natural logarithm
of the number of bytes. Compute the sample mean and sample variance of
y1, . . . , y20*

*In the remaining parts, assume the yis have a normal sampling
distribution with mean μ and variance σ2*

``` r
# set y as length of data on log scale
y <- log(data$bytes)

#output
cat("Mean:", mean(y), "bytes\n")
```

    ## Mean: 8.050194 bytes

``` r
cat("variance:", var(y), "bytes\n")
```

    ## variance: 1.147371 bytes

## 2c.

*Assume σ2 is known to equal the sample variance. Consider a flat prior
for μ. Use it to:*

*(i) \[3 pts\] Compute the posterior mean of μ, posterior variance of μ,
and posterior precision of μ.*

``` r
y <- log(data$bytes)
n <- length(y) #size
y_bar <- mean(y) #mean
sigma2 <- var(y) #variance
```

Since the log-transformation length is close to Normal Distribition.
Will assume **likelihood distibution** to be ~ N(mean(y),var(y)).

Consider a **flat prior** for μ and known σ2, the posterior distribution
should be: **μ\|y ~ Normal(y_bar, σ²/n)**

``` r
post_mean <- y_bar
post_variance <- sigma2/2
post_precision <- 1/post_variance

#output
cat("Posterior_Mean:", post_mean, "bytes\n")
```

    ## Posterior_Mean: 8.050194 bytes

``` r
cat("Posterior_Variance:", post_variance, "bytes\n")
```

    ## Posterior_Variance: 0.5736854 bytes

``` r
cat("Posterior_Precision:", post_precision, "bytes\n")
```

    ## Posterior_Precision: 1.743116 bytes

*(ii) \[3 pts\] Plot a prior density of μ and a posterior density of μ
together in a single plot. Label which density is which.*

``` r
# SD
post_sd = sqrt(post_variance)

# Generate X values for density
x_vals <- seq(post_mean - 3*post_sd, post_mean + 3*post_sd, length.out = 1000)

# Calculate density
prior_density <- rep(1/diff(range(x_vals)),length(x_vals))
post_density <- dnorm(x_vals, mean = post_mean, sd = post_sd)
```

Now is the plot…

``` r
# Simple Plot
plot(x_vals,post_density,
    type = 'l',
    col = "blue",
    xlab = "μ",
    ylab = "Density",
    main = "Prior and Posterial Distribution of Wiki Data")

# Add Flat prior
abline(h = prior_density, col = "red", lwd = 2)
```

![](assignment_1_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

3)  \[2 pts\] Compute a 95% central posterior interval for μ.

``` r
# Utility Function for posterior Interval
calculate_posterior_interval <- function(post_mean, post_sd, conf_level = 0.95) {
    # Calculate alpha for the confidence level
    alpha <- (1 - conf_level) / 2
    
    # Calculate the lower and upper bounds
    lower_bound <- qnorm(alpha, mean = post_mean, sd = post_sd)
    upper_bound <- qnorm(1 - alpha, mean = post_mean, sd = post_sd)
    
    return(c(lower = lower_bound, upper = upper_bound))
}

# Output
interval <- calculate_posterior_interval(post_mean, post_sd)
cat("95% Central Posterior Interval:", "\n")
```

    ## 95% Central Posterior Interval:

``` r
cat("Lower bound:", round(interval[1], 4), "\n")
```

    ## Lower bound: 6.5657

``` r
cat("Upper bound:", round(interval[2], 4), "\n")
```

    ## Upper bound: 9.5347

## 2d.

*Now treat σ2 as unknown, and let μ and σ2 have prior p(μ, σ2) ∝ (σ2)−1
σ2 \> 0*

1)  \[3 pts\] Compute the posterior mean of μ, posterior variance of μ,
    and posterior precision of μ. (If you cannot compute explicitly, use
    a good computational approximation.)

Recall information above:

``` r
y <- log(data$bytes)
n <- length(y) #size
y_bar <- mean(y) #mean
sigma2 <- var(y) #variance
```

Follow non-informative join prior (Slide 2.2 Week 2), we have

``` r
post_mean_mu <- y_bar
post_variance_mu <- sigma2/n
post_precision_mu <- n/post_variance

#output
cat("Posterior_Mean_of_μ:", post_mean_mu, "bytes\n")
```

    ## Posterior_Mean_of_μ: 8.050194 bytes

``` r
cat("Posterior_Variance_of_μ:", post_variance_mu, "bytes\n")
```

    ## Posterior_Variance_of_μ: 0.05736854 bytes

``` r
cat("Posterior_Precision_μ:", post_precision_mu, "bytes\n")
```

    ## Posterior_Precision_μ: 34.86231 bytes

2)  \[2 pts\] Approximate a 95% central posterior interval for μ. (Make
    sure your approximation is reasonably accurate.)

3)  \[2 pts\] Approximate a 95% central posterior interval for σ2. (Make
    sure your approximation is reasonably accurate.)

## 2e.
