Lab09
================
VY
2022-10-26

## Question 2

``` r
set.seed(3000)
fun1 <- function(n = 100, k = 4, lambda = 4) {
  x <- NULL
  
  for (i in 1:n)
    x <- rbind(x, rpois(k, lambda))
  
  return(x)
}

f1 <-fun1(1000,4)
mean(f1)
```

    ## [1] 4.08125

``` r
fun1alt <- function(n = 100, k = 4, lambda = 4) {
  # YOUR CODE HERE
  x <- matrix( rpois(n*k, lambda) , ncol=4)
  
  return(x)
}
f1 <- fun1alt(50000,4)

# Benchmarking
microbenchmark::microbenchmark(
  fun1(),
  fun1alt()
)
```

    ## Unit: microseconds
    ##       expr     min       lq      mean   median       uq      max neval
    ##     fun1() 463.687 706.6240 851.74489 809.4315 961.2835 2163.921   100
    ##  fun1alt()  30.861  35.3075  83.81234  40.4840  45.9470 4108.045   100

# Function: Find column max

``` r
# Data Generating Process (10 x 10,000 matrix)
set.seed(1234)
x <- matrix(rnorm(1e4), nrow=10)

# Find each column's max value
fun2 <- function(x) {
  apply(x, 2, max)
}


fun2alt <- function(x) {
  # YOUR CODE HERE
 idx <- max.col(t(x))
 x[cbind(idx,1:4)]
}


# Benchmarking
microbenchmark::microbenchmark(
  fun2(x),
  fun2alt(x)
)
```

    ## Unit: microseconds
    ##        expr      min       lq      mean    median       uq      max neval
    ##     fun2(x) 1445.457 1705.529 2141.9662 2019.7775 2241.073 6821.737   100
    ##  fun2alt(x)  168.803  226.131  323.9173  255.4525  335.206 4557.163   100

# Bootstrapping

``` r
my_boot <- function(dat, stat, R, ncpus = 1L) {
  
  # Getting the random indices
  n <- nrow(dat)
  idx <- matrix(sample.int(n, n*R, TRUE), nrow=n, ncol=R)
 
  # Making the cluster using `ncpus`
  # STEP 1:
  cl <- makePSOCKcluster(4)
  clusterSetRNGStream(cl, 123) # Equivalent to `set.seed(123)`

  # STEP 2: GOES HERE
  clusterExport(cl, c("stat", "dat", "idx"), envir = environment())

    # STEP 3: THIS FUNCTION NEEDS TO BE REPLACES WITH parLapply
  ans <- parLapply(cl, seq_len(R), function(i) {
    stat(dat[idx[,i], , drop=FALSE])
  })
  
  # Coercing the list into a matrix
  ans <- do.call(rbind, ans)
  
  # STEP 4: GOES HERE
  
  ans
  
}
```

# run in parallel

``` r
# Bootstrap of an OLS
my_stat <- function(d) coef(lm(y ~ x, data=d))

# DATA SIM
set.seed(1)
n <- 500; R <- 1e4

x <- cbind(rnorm(n)); y <- x*5 + rnorm(n)

# Checking if we get something similar as lm
ans0 <- confint(lm(y~x))
ans1 <- my_boot(dat = data.frame(x, y), my_stat, R = R, ncpus = 2L)

# You should get something like this
t(apply(ans1, 2, quantile, c(.025,.975)))
```

    ##                   2.5%      97.5%
    ## (Intercept) -0.1386903 0.04856752
    ## x            4.8685162 5.04351239

``` r
##                   2.5%      97.5%
## (Intercept) -0.1372435 0.05074397
## x            4.8680977 5.04539763
ans0
```

    ##                  2.5 %     97.5 %
    ## (Intercept) -0.1379033 0.04797344
    ## x            4.8650100 5.04883353

``` r
##                  2.5 %     97.5 %
## (Intercept) -0.1379033 0.04797344
## x            4.8650100 5.04883353
```
