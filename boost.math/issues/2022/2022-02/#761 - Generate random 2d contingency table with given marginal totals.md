# #761 - Generate random 2d contingency table with given marginal totals [Open]

> Username: HDembinski  
> Created at: 2022-02-15 09:50:57 UTC  
> Updated at: 2022-09-26 16:14:43 UTC  
> Url: https://github.com/boostorg/math/issues/761  

## Motivation  
  
Sometimes you have pairs of values x, y and you want to know whether y is independent of x or not. People sometimes compute the correlation of x and y, but independence is a stronger statement than lack of correlation. It is possible to have distributions with zero correlation that are nevertheless not independent, e.g. when y = x^2 for x distributed symmetrically around 0.  
  
Some tests, like the popular [USP test](https://royalsocietypublishing.org/doi/10.1098/rspa.2021.0549), compute a test statistic and then use Monte-Carlo to compute the distribution of the test statistic under the null hypothesis (independence). For that, one needs to generate random 2D tables ("histograms") that have the same row and column totals as the original data. The probability distribution for such a table has been worked out in the 80'ies, and Patefield found an elegant non-trivial algorithm to randomly sample from this distribution.  
  
## Candidate implementation  
  
I searched the net for implementations in C/C++ of Patefield's algorithm, but only found implementations under GPL license. I want to have the code under a less restrictive license and so I translated the published FORTRAN code to C from scratch and [based my implementation on that](https://github.com/resample-project/resample/blob/develop/src/rcont.c). The code is currently licensed under BSD 3-clause. I would also like to release it under BSL and contribute it to Boost.Math.  
  
My implementation already evolved from the original and has cool features that the original implementation lacks. The changes are documented in the code. Sorry for all the `goto`s, it is how the original code was written. In time, I am interested in removing the `goto`s step by step, but for now it is more important to have a baseline code that works.  
  
In addition to Patefield's algorithm, I also want to add a simple shuffling algorithm. The shuffling algorithm requires additional memory, but is faster than Patefield in certain scenarios.  
  
## Implementation discussion  
  
I am not yet intimately familiar with the distribution infrastructure in Boost.Math. On a quick glance, I could not find a distribution that returns more than a single number. For this algorithm, the random variate is a 2d array. We should allow the user to allocate the memory for this array only once and then run the algorithm as many times as they want, which should fill the user-provided object with new values. That does not seem to fit into the current design. How should this be approached?

---

## Comment 1

> Username: NAThompson  
> Created at: 2022-02-15 16:26:44 UTC  
> Updated at: 2022-02-15 16:34:29 UTC  
> Url: https://github.com/boostorg/math/issues/761#issuecomment-1040485496  

> We should allow the user to allocate the memory for this array only once and then run the algorithm as many times as they want, which should fill the user-provided object with new values. That does not seem to fit into the current design. How should this be approached?  
  
Sometimes you can get NRVO for containers and the memory will be reused, even if it "looks" allocated every call. Could a std::array be used?  
  
I was "reserving" all 2D array work for C++23, where we could have an `A[i,j]` syntax . . .  
  
> Sometimes you have pairs of values x, y and you want to know whether y is independent of x or not. People sometimes compute the correlation of x and y, but independence is a stronger statement than lack of correlation.  
  
Internally we were discussing implementing a `chatterjee_correlation` for this problem: https://arxiv.org/abs/1909.10140; see figure 2-I think it solves your problem.

---

## Comment 2

> Username: HDembinski  
> Created at: 2022-02-15 20:14:22 UTC  
> Updated at: 2022-02-15 21:01:22 UTC  
> Url: https://github.com/boostorg/math/issues/761#issuecomment-1040747149  

Thanks for the pointer to the Chatterjee coefficient, very interesting. From reading the abstract, it sounds great. However, the USP test statistic also has some proven optimal properties. Considering how much work I put into this algorithm, I want it to be preserved either way.  
  
I don't see why you want to wait for the array syntax from C++23.  
  
Regarding NVRO, I don't see how this allows the compiler to skip creating a temporary completely. You can create the array in the function and return it without calling the copy ctr, but what happens when the outer code assigns to an existing array class?  
  
Regarding std::array, I think you want to design the library so that it works also with dynamically sized containers. The random table is 2D, I think std::array is only 1D (ok, nevermind, I thought that you cannot make `std::array<std::array<int, 3>, 3>`, but I was mistaken).

---

## Comment 3

> Username: HDembinski  
> Created at: 2022-02-15 20:57:42 UTC  
> Url: https://github.com/boostorg/math/issues/761#issuecomment-1040785502  

On a second look, the Chatterjee coefficient seems to be related to the [Kendall rank correlation](https://en.wikipedia.org/wiki/Kendall_rank_correlation_coefficient). As it is stated in the paper, the primary purpose of the coefficient is not to test for independence, but that is precisely what the USP test is for. Unless the opposite is proven, I will assume that the USP test is more powerful than a test based on Chatterjee.  
  
Another advantage of the USP test over rank correlation coefficients is that it uses histograms of the data instead of the individual data pairs. I work in high-energy physics, where we have Big Data. Putting all the value pairs into a histogram is convenient and standard practice, more so than working with the individual value pairs. I can still apply the USP test on the histogram, but I cannot compute a rank correlation coefficient from binned data.  
  
Bottom line: I think it is clear that the algorithms that I propose here are generally useful. Are you convinced now or should I continue talking?

---

## Comment 4

> Username: NAThompson  
> Created at: 2022-02-15 21:47:26 UTC  
> Url: https://github.com/boostorg/math/issues/761#issuecomment-1040827754  

> Are you convinced now or should I continue talking?  
  
I'm convinced.

---

## Comment 5

> Username: HDembinski  
> Created at: 2022-09-26 15:56:11 UTC  
> Updated at: 2022-09-26 16:00:51 UTC  
> Url: https://github.com/boostorg/math/issues/761#issuecomment-1258264362  

@NAThompson   
Sorry for the long break, I am now coming back to this. Great to hear this. Note that I also want [to include the algorithm in scipy](https://github.com/scipy/scipy/issues/15618). I think the ideal solution is to add it to Boost.Math and then wrap that to Python via scipy. Scipy already uses Boost.Math.  
  
So where should I put this code? In a way, this is a special random distribution, which generates 2d matrices instead of numbers. It would fit best into boost/math/distributions  
  
As far as I can see, there is no precedent for a multivariate distribution there.  
  
The return type of such a distribution is a table, but we probably also want to offer some interface where the user provides a  matrix object which the algorithm just fills.

---

## Comment 6

> Username: HDembinski  
> Created at: 2022-09-26 16:14:43 UTC  
> Url: https://github.com/boostorg/math/issues/761#issuecomment-1258288394  

I think we need some kind of matrix_view over all possible contiguous array types, like [mdspan](https://en.cppreference.com/w/cpp/container/mdspan).
