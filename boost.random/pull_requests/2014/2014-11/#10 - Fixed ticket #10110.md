# #10 Fixed ticket #10110 [Closed]

> Username: maximiliank  
> Created at: 2014-11-18 16:15:11 UTC  
> Updated at: 2023-05-13 17:28:42 UTC  
> Closed at: 2023-05-13 17:28:42 UTC  
> Url: https://github.com/boostorg/random/pull/10  

Fixed https://svn.boost.org/trac/boost/ticket/10110  
Negative binomial distribution works now for real positive parameters k.  
Added assertions on the parameters, i.e. k > 0 and 0 < p < 1.

---

## Comment 1

> Username: swatanabe  
> Created_at: 2023-05-13 17:28:42 UTC  
> Url: https://github.com/boostorg/random/pull/10#issuecomment-1546715539  

This is a breaking change and makes it incompatible with `std::negative_binomial_distribution`.

---
