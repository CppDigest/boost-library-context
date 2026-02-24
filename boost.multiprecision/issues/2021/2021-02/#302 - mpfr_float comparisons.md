# #302 - mpfr_float comparisons [Closed]

> Username: jzmaddock  
> Created at: 2021-02-25 19:43:20 UTC  
> Updated at: 2021-03-20 17:02:27 UTC  
> Closed at: 2021-03-20 17:02:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/302  

Should probably NOT be noexcept.  
  
There are also native comparisons available for more types (float, double etc).

---

## Comment 1

> Username: anishsingh935  
> Created at: 2021-03-19 12:54:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/302#issuecomment-802812757  

Hi  can you please explain this issue ,exactly what is bug

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-03-20 17:02:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/302#issuecomment-803411822  

Ooops, this was fixed in https://github.com/boostorg/multiprecision/pull/303.  
  
The bug was that the comparison operators for mpfr_float were marked as noexcept, and in general we can't guarantee that is the case since we don't know what mpfr is doing internally.
