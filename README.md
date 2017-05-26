<img src="README_files/top_banner.png" width="4032" />

greta is an R package for writing statistical models and fitting them by MCMC, it's:

**easy** - greta models can be [written interactively](#example) in R, so there's no need to learn a new language like BUGS or Stan and if you make a mistake you get [feedback](#feedback) immediately, not from a compiler.

**scalable** - greta does [Hamiltonian Monte Carlo](http://onlinelibrary.wiley.com/doi/10.1111/2041-210X.12681/full) and uses Google's [TensorFlow](https://www.tensorflow.org/) computational engine, meaning it's particularly fast on big datasets and can run in parallel across lots of CPUs, or on a GPU.

**extensible** - because greta is written in R, you can define your own [functions](#functions) and [modules](#modules) to add new methods.

To get started with greta, take a look at the example below then check out the [getting started](https://rawgit.com/goldingn/greta/dev/inst/doc/getting_started.html) tutorial. There are also several [example models](https://rawgit.com/goldingn/greta/dev/inst/doc/example_models.html), with equivalent BUGS and Stan code. If you'd like to understand how greta works under the hood, check out [How does this work?](https://rawgit.com/goldingn/greta/dev/inst/doc/how_does_this_work.html)

<img src="README_files/banner.png" width="940" />

### Example

Here's a simple Bayesian linear regression model for the sepal length variable in R's `iris` dataset.

``` r
library(greta)

# create parameters, stating their prior distributions
intercept = normal(0, 5)
coefficient = normal(0, 3)
sd = lognormal(0, 3)

# write the equation for the expected mean sepal length
mean <- intercept + coefficient * iris$Petal.Length

# define the likelihood of the observed data
distribution(iris$Sepal.Length) = normal(mean, sd)
```

With the model written, we can draw samples of the parameters we care about.

``` r
model <- define_model(intercept, coefficient, sd)

draws <- mcmc(model, n_samples = 500)
```

This outputs an `mcmc.list` object, so you can plot and summarise the samples using your favourite MCMC visualisation software.

``` r
library(MCMCvis)

MCMCtrace(draws)
MCMCplot(draws, xlim = c(-1, 5))
```

<img src="README_files/figure-markdown_github/vis-1.png" width="400px" /><img src="README_files/figure-markdown_github/vis-2.png" width="400px" />

<img src="README_files/banner.png" width="940" />

### Installation

greta can be installed from GitHub using the devtools package

``` r
devtools::install_github('goldingn/greta')
```

or, you can install the development version with:

``` r
devtools::install_github('goldingn/greta@dev')
```

however greta depends on TensorFlow (version 1.0.0 or higher) which will need to be successfully installed before greta will work. See [here](https://www.tensorflow.org/install/) for instructions on installing TensorFlow.

<img src="README_files/banner.png" width="940" />

### How fast is it?

For small to medium size (a few hundred data points) problems, Stan will probably be faster than greta. Where the model involves thousands of datapoints or multiplication of large matrices, greta is likely to be faster than STAN. That's because TensorFlow is heavily optimised for linear algebra operations.

For example, the example code above takes around 30 seconds to run on my laptop for the 150-row iris data. If you run the same model and sampler on a dataset of 15,000 rows, it still only takes around 35 seconds. That's not bad. Not bad at all.

Since TensorFlow can be run across multiple CPUs or on a GPU, greta models can be made to scale to massive datasets. I'll add some benchmarks soon to give a clearer idea of how greta compares with other MCMC software.

<img src="README_files/banner.png" width="940" />

### Why 'greta'?

There's a recent convention of naming probabilistic modelling software after pioneers in the field (e.g. [STAN](https://en.wikipedia.org/wiki/Stanislaw_Ulam) and [Edward](https://en.wikipedia.org/wiki/George_E._P._Box)).

[Grete Hermann](https://en.wikipedia.org/wiki/Grete_Hermann) wasn't a probabilist, but she wrote [the first algorithms](http://dl.acm.org/citation.cfm?id=307342&coll=portal&dl=ACM) for computer algebra; in the 1920s, well before the first electronic computer was built. This work laid the foundations for computer algebra libraries (like TensorFlow) that enable modern probabilistic modelling.

In case that's not enough reason to admire her, Grete Hermann also [disproved a popular theorem in quantum theory](https://arxiv.org/pdf/0812.3986.pdf) and was part of the German resistance against the Nazi regime prior to World War Two.

Grete (usually said *Greh*•tuh, like its alternate spelling *Greta*) can be confusing for non-German speakers to pronounce, so I've taken the liberty of naming the package greta instead. You can call it whatever you like.

<img src="README_files/banner.png" width="940" />

#### software

greta relies on some pretty incredible pieces of software, including Rstudio's [`reticulate`](https://github.com/rstudio/reticulate) and [`tensorflow`](https://rstudio.github.io/tensorflow/) packages, which bring Google TensorFlow and all things python to R. Under the hood, greta also uses Winston Chang's [`R6`](https://github.com/wch/R6) object system.

The design and scope of greta was inspired by other general-purpose MCMC software like [BUGS](http://www.openbugs.net/) and [JAGS](http://mcmc-jags.sourceforge.net/), but particularly by [Stan](http://mc-stan.org/). The python package [Edward](http://edwardlib.org/) also uses TensorFlow as a backend for general-purpose statistical modelling, as does [GPflow](https://github.com/GPflow/GPflow), which was a source of inspiration for how greta is implemented.

<img src="README_files/banner.png" width="940" />

### Contributors

[![Build Status](https://travis-ci.org/goldingn/greta.svg?branch=master)](https://travis-ci.org/goldingn/greta) [![codecov.io](https://codecov.io/github/goldingn/greta/coverage.svg?branch=master)](https://codecov.io/github/goldingn/greta?branch=master) [![cran version](http://www.r-pkg.org/badges/version/greta)](https://cran.rstudio.com/web/packages/greta)

I would welcome contributions to this project from anyone with time to spare! The issues tracker lists a number of known bugs and extensions I have planned. Please feel free to add to those any bugs or issues you come across, or let me know if you'd like to help fix some of them or add new features.

#### modules

greta has a basic module system to package up more 'niche' functionality. Check out `?dynamics` for an example of a module for stage-structured dynamical models. I'm still working out whether these modules should be kept in this package, or split out into one or more separate packages. Either way I would be keen for people to contribute new modules!

<img src="README_files/bottom_banner.png" width="940" />
