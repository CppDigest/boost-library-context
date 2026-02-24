# #245 - max_digits10 + mpfr hangs [Closed]

> Username: NAThompson  
> Created at: 2020-06-05 22:27:56 UTC  
> Updated at: 2020-12-26 16:31:42 UTC  
> Closed at: 2020-12-26 16:31:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/245  

In the representations [PR](https://github.com/boostorg/multiprecision/pull/244), we get a hang unless we comment out  
  
```cpp  
out << std::setprecision(std::numeric_limits<Real>::max_digits10);  
```  
  
`cpp_bin_float` does not hang . . .

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-06-06 08:04:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/245#issuecomment-640009261  

That's weird, it's just an integer constant with value INT_MAX for `mpfr_float`.  Can you preprocess the source and make sure that the definition for numeric_limits<>::max_digits10 is in the output?  Should be mpfr.hpp:2614, but is conditional on `BOOST_NO_INCLASS_MEMBER_INITIALIZATION` (which should *not* be set in the cases you mention).

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-06-06 12:00:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/245#issuecomment-640048637  

Oh.... sorry, I see the issue, you're setting the precision of the stream to INT_MAX.  The issue is that there is no sane value for this constant for variable precision types?

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-06-15 14:52:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/245#issuecomment-644183542  

As a workaround, I have the following:  
  
```  
    constexpr int p = std::numeric_limits<Real>::max_digits10;  
    out << std::setprecision(p);  
    if constexpr (p == 2147483647) {  
        out << std::setprecision(rep.x_.backend().precision());  
    }  
```
