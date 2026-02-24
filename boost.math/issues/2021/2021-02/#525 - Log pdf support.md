# #525 - Log pdf support? [Closed]

> Username: Svalorzen  
> Created at: 2021-02-09 12:41:37 UTC  
> Updated at: 2022-04-19 01:45:50 UTC  
> Closed at: 2022-04-19 01:45:50 UTC  
> Url: https://github.com/boostorg/math/issues/525  

I was wondering whether there is, or there are plans to add, an interface to compute the `logpdf`s to the random distributions.  
  
This would allow to more accurately manipulate probabilities without the need to first apply a log function to the output of the `pdf` and `cdf` functions.

---

## Comment 1

> Username: HDembinski  
> Created at: 2022-02-15 09:14:55 UTC  
> Updated at: 2022-02-15 20:16:20 UTC  
> Url: https://github.com/boostorg/math/issues/525#issuecomment-1040034666  

+1 on this. When using the log-likelihood method, you need the logpdf instead of the pdf. Many common distributions are of the exponential family, so the logpdf is calculated naturally internally, and code that computes the log-likelihood then effectively does the superfluous log(exp(logpdf)).  
  
The logpdf is also needed when the log-likelihood method is applied to additive models of the form pdf = z pdf_A + (1-z) pdf_B. There is a numerically stable way to compute log(exp(x1) + exp(x2)) (see https://numpy.org/doc/stable/reference/generated/numpy.logaddexp.html#numpy.logaddexp) where x1 and x2 are logpdf values.

---

## Comment 2

> Username: NAThompson  
> Created at: 2022-02-15 16:26:00 UTC  
> Url: https://github.com/boostorg/math/issues/525#issuecomment-1040484659  

A fast, numerically stable log-sum-exp seems like the natural place to start with this. I don't have much context for this problem; are there enough terms that it should it be parallelized?  
  
@mborland

---

## Comment 3

> Username: mborland  
> Created at: 2022-02-15 17:03:00 UTC  
> Url: https://github.com/boostorg/math/issues/525#issuecomment-1040532176  

Is the linked PR the first step of what you are looking for @HDembinski? Like you said it's not overly difficult to add logpdf support by distribution.

---

## Comment 4

> Username: NAThompson  
> Created at: 2022-02-15 17:03:40 UTC  
> Updated at: 2022-02-15 17:03:50 UTC  
> Url: https://github.com/boostorg/math/issues/525#issuecomment-1040534921  

@mborland : I think the hard part is getting the ULPs plots for all of these correct . . . but I think this is indeed a good start.

---

## Comment 5

> Username: HDembinski  
> Created at: 2022-02-15 20:22:20 UTC  
> Url: https://github.com/boostorg/math/issues/525#issuecomment-1040754549  

> Is the linked PR the first step of what you are looking for @HDembinski? Like you said it's not overly difficult to add logpdf support by distribution.  
  
Yes, although the arcsine distribution is not the first that comes to mind. Ideal targets are the normal, poisson, exponential, beta, gamma, ...
