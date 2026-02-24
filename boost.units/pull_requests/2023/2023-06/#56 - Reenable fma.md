# #56 Reenable fma [Open]

> Username: NAThompson  
> Created at: 2023-06-01 01:08:48 UTC  
> Updated at: 2023-06-03 21:51:57 UTC  
> Url: https://github.com/boostorg/units/pull/56  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2023-06-01 01:09:19 UTC  
> Updated_at: 2023-06-01 04:28:14 UTC  
> Url: https://github.com/boostorg/units/pull/56#issuecomment-1571169215  

@mborland, @jzmaddock, @swatanabe  : Could your review?  
  
Couldn't figure out how to get this one to compile:  
  
```  
BOOST_TEST(bu::fma(L1, 1.0/L1, 1.0) == 2.0);  
```  
  
but . . . perfect is the enemy of the good.

---

## Comment 2

> Username: mborland  
> Created_at: 2023-06-01 11:12:00 UTC  
> Url: https://github.com/boostorg/units/pull/56#issuecomment-1571850837  

It seems reasonable to me. I think something of note if it's not already known is this transitively requires C++14 since `cmath.hpp` includes a number of headers from Boost.Math.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2023-06-01 17:48:18 UTC  
> Url: https://github.com/boostorg/units/pull/56#issuecomment-1572524196  

If you wanted to handle all of the mixed argument cases (there are a lot!), then the only sensible way to do it is to provide **one** fma template that is enable_if'd on at least one of the template parameters being a unit.  Then in the body you need to call an "unwrap" helper function that returns the result of argN.value() when argN is a unit, and simply argN otherwise.  Hope that all makes sense!

---

## Comment 4

> Username: swatanabe  
> Created_at: 2023-06-02 00:24:05 UTC  
> Url: https://github.com/boostorg/units/pull/56#issuecomment-1572954831  

LGTM.  
  
The other mixed argument cases are not necessary. Multiplication of a   
quantity by a scalar is allowed. Addition of a quantity and a scalar is not.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2023-06-02 04:28:22 UTC  
> Updated_at: 2023-06-02 04:29:05 UTC  
> Url: https://github.com/boostorg/units/pull/56#issuecomment-1573127884  

> The other mixed argument cases are not necessary.   
  
These were added due to a compile error I observed on a Horner's algorithm where the abscissa is dimensionless and the coefficients are dimensioned. The compile error when only enabling the original overload is as follows:  
  
```  
In file included from ~/test/polynomial_test.cpp:22:  
~/polynomial.hpp:58:11: fatal error: no matching function for call to 'fma'  
      y = fma(y, x, c_[i]);  
          ^~~  
~/.conan2/p/boost21487c6284710/p/include/boost/units/cmath.hpp:242:1: note: candidate template ignored: could not match 'quantity<Unit2, Y>' against 'double'  
fma BOOST_PREVENT_MACRO_SUBSTITUTION (const quantity<Unit1,Y>& q1,  
^  
```  
  
Code:  
  
```  
  template<typename Real>  
  [[nodiscard]] CoefficientType operator()(Real x) const noexcept {  
    using std::fma;  
    CoefficientType y = c_.back();  
    long n = c_.size() - 1;  
    for (long i = n - 1; i >= 0; --i) {  
      y = fma(y, x, c_[i]);  
    }  
    return y;  
  }  
```  
  
Compiler:  
  
```  
g++ --version  
Apple clang version 14.0.3 (clang-1403.0.22.14.1)  
Target: arm64-apple-darwin22.5.0  
Thread model: posix  
InstalledDir: /Library/Developer/CommandLineTools/usr/bin  
```

---

## Comment 6

> Username: NAThompson  
> Created_at: 2023-06-03 21:51:56 UTC  
> Url: https://github.com/boostorg/units/pull/56#issuecomment-1575214465  

@swatanabe : Mind merging?

---
