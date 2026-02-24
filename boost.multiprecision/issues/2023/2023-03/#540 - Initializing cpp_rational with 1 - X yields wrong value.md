# #540 - Initializing cpp_rational with 1 / X yields wrong value [Closed]

> Username: Xirema  
> Created at: 2023-03-10 01:13:39 UTC  
> Updated at: 2023-03-11 09:28:47 UTC  
> Closed at: 2023-03-11 09:28:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/540  

It appears this has been a problem since 1.78.0, and is still affecting the current version of multiprecision.  
  
Attempting to construct a `cpp_rational` with a rational value of 1 divided by a `cpp_int` value of anything else results in the final value being 1, instead of the correct value.  
  
This can be worked-around by ensuring that the divided-by value is explicitly cast to a `cpp_rational` type before performing the division.  
  
Curiously, this only occurs if the Numerator is 1; if the Numerator is any other value, the rational is correctly initialized.  
  
[Godbolt Link demonstrating that the behavior was different (correct) in 1.77.0 and persists up through 1.81.0](https://godbolt.org/z/o96954M7n)  
  
Sample Code to reproduce the issue:  
```  
#include<boost/multiprecision/cpp_int.hpp>  
#include<iostream>  
  
int main() {  
    using Integer = boost::multiprecision::cpp_int;  
    using Rational = boost::multiprecision::cpp_rational;  
  
    Integer one = 1;  
    Integer tenThousand = 10000;  
    Integer threeFourFiveSix = 3456;  
    Rational oneInTenThousand = Rational(one) / tenThousand;  
    Rational oneInThreeFourFiveSix = Rational(one) / threeFourFiveSix;  
  
    std::cout << one << "\n" << tenThousand << "\n" << oneInTenThousand << "\n";  
    std::cout << oneInThreeFourFiveSix << "\n";  
  
    Integer two = 2;  
    Rational twoInTenThousand = Rational(two) / tenThousand;  
    std::cout << twoInTenThousand << "\n";  
  
    Rational oneInTenThousandCorrected = Rational(one) / Rational(tenThousand);  
    std::cout << oneInTenThousandCorrected << "\n";  
}  
```  
Output in 1.77.0:  
```  
1  
10000  
1/10000  
1/3456  
1/5000  
1/10000  
```  
Output in 1.81.0:  
```  
1  
10000  
1  
1  
1/5000  
1/10000  
```

---

## Comment 1

> Username: mborland  
> Created at: 2023-03-10 18:32:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/540#issuecomment-1464222085  

In 1.78.0 the rational adapter was changed [here](https://github.com/boostorg/multiprecision/pull/366). This looks to be similar to #541 where implicit conversions were dropped by the change. @jzmaddock would know best.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2023-03-10 18:43:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/540#issuecomment-1464235778  

Testing a fix now.
