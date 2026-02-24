# #265 - small problem with boost::multiprecision::frexp for MPFR only. [Closed]

> Username: cosurgi  
> Created at: 2020-08-10 20:09:40 UTC  
> Updated at: 2020-09-04 09:28:31 UTC  
> Closed at: 2020-09-04 09:28:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/265  

Try this code:  
  
```  
#include <boost/multiprecision/mpfr.hpp>  
#include <iostream>  
  
int main()  
{  
	boost::multiprecision::mpfr_float_500 val("-0.999999999999999999999999778370088952043740344504867972909872539207515981285392968364222963125347205961928119872988781594713043198125331291");  
	int b=0;  
	std::cout << std::setprecision(80) << boost::multiprecision::frexp(val,&b) << "\n";  
}  
```  
  
you will get:  
  
```  
-0.49999999999999999999999988918504447602187017225243398645493626960375799064269648  
```  
  
The problem is that frexp promises in specification to return value between [0.5 and 1](https://en.cppreference.com/w/cpp/numeric/math/frexp):  
  
> if no errors occur, returns the value x in the range (-1;-0.5], [0.5; 1)  
  
And I needed to write a code which depends on this promise. So for now I had to implement a simple workaround:  
  
```  
    // in here the norm is positive always.  
    if ((norm > 0.25) and (norm < 0.5)) { // if this bug becomes more annoying this fix might need to get moved into MathFunctions.hpp  
        norm *= 2;  
        exponent -= 1;  
    }  
```  
  
Complete code for this part is here: https://gitlab.com/yade-dev/trunk/-/blob/bitCorrectness/py/high-precision/_RealHPDiagnostics.cpp#L50  
  
honestly I'm not sure if I should submit it here or to MPFR :) maybe both?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-08-11 15:36:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/265#issuecomment-672021831  

My bad.  I'm using the wrong mpfr function I think.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-08-12 10:45:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/265#issuecomment-672797791  

Fixes in https://github.com/boostorg/multiprecision/pull/266.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2020-09-04 09:28:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/265#issuecomment-687035389  

Merged.
