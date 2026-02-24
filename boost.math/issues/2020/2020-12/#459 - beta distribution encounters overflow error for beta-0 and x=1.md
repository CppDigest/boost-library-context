# #459 - beta distribution encounters overflow error for beta<0 and x=1 [Closed]

> Username: mckib2  
> Created at: 2020-12-20 03:24:19 UTC  
> Updated at: 2020-12-24 20:06:23 UTC  
> Closed at: 2020-12-24 20:06:23 UTC  
> Url: https://github.com/boostorg/math/issues/459  

The beta distribution PDF has interesting cases at x=0 and x=1.  x=0 seems to be handled correctly, but when beta<1, x=1 it throws an overflow error:  
```cpp  
#include <iostream>  
#include <boost/math/distributions/beta.hpp>  
  
int main() {  
    auto d = boost::math::beta_distribution<double>(2.3098496451481823, 0.6268795430096368);  
    std::cout << "pdf(1.0): " << boost::math::pdf(d, 1.0) << std::endl;  
    return 0;  
}  
```  
  
Error:  
```  
terminate called after throwing an instance of 'boost::wrapexcept<std::overflow_error>'  
  what():  Error in function ibeta_derivative<double>(double,double,double): Overflow Error  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-12-20 12:22:29 UTC  
> Url: https://github.com/boostorg/math/issues/459#issuecomment-748600847  

Confirmed.

---

## Comment 2

> Username: mborland  
> Created at: 2020-12-20 12:52:37 UTC  
> Url: https://github.com/boostorg/math/issues/459#issuecomment-748603969  

@jzmaddock I have a PR about ready for this one.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2020-12-20 16:18:01 UTC  
> Url: https://github.com/boostorg/math/issues/459#issuecomment-748628271  

@mborland : that was my first thought, but I don't think that's quite the right fix.  We need to address this upstream in ibeta_derivative here: https://github.com/boostorg/math/blob/db42e6bced785fa5d7fcb92ad7406f139a4ca10b/include/boost/math/special_functions/beta.hpp#L1414  
  
But hold on, now that I look at this again, if the PDF is:  
  
`x^(a-1) (x-1)^(b-1) / beta(a, b)`  
  
Then surely that *is* infinite for x == 1, b < 1 ?  
  
I'm sure I'm missing something obvious though....

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-12-20 16:21:35 UTC  
> Url: https://github.com/boostorg/math/issues/459#issuecomment-748628646  

Ah... my bad, the answer is here: https://www.wolframalpha.com/input/?i=PDF%5BBetaDistribution%5Ba%2C+b%5D%2C+x%5D, the PDF is defined as zero at x >= 1 and the usual formula no longer applies.  OK, your fix is correct then :)  Thanks Matt!

---

## Comment 5

> Username: mckib2  
> Created at: 2020-12-20 20:15:09 UTC  
> Url: https://github.com/boostorg/math/issues/459#issuecomment-748665357  

#460  appears to fix the issue, thanks for looking into this!
