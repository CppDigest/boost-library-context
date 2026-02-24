# #416 - fixed << setprecision(0) displays too many digits of a float128 [Closed]

> Username: cffk  
> Created at: 2022-01-20 20:04:04 UTC  
> Updated at: 2022-02-13 09:06:55 UTC  
> Closed at: 2022-02-13 09:06:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/416  

I expect  
```c++  
cout << fixed << setprecision(0) << x << '\n';  
```  
to print `x` rounded to the nearest integer.  This is what happens if `x` is a `double`; but if it's a `float128` then "as many digits as you've got" are printed.  
  
This is illustrated by the following code  
```c++  
#include <iostream>  
#include <boost/multiprecision/float128.hpp>  
  
int main() {  
  double x = 9.1234;  
  boost::multiprecision::float128 y = 9.1234Q;  
  std::cout << std::fixed;  
  for (int i = 4; i >= 0; --i)  
    std::cout << std::setprecision(i) << x << " " << y << "\n";  
}  
```  
which produces  
```  
9.1234 9.1234  
9.123 9.123  
9.12 9.12  
9.1 9.1  
9 9.123400000000000000000000000000000030  
```  
  
This issue is a copied from  
  
https://svn.boost.org/trac10/ticket/10103  
  
from 8 years ago.  I've worked around this bug long ago.  But perhaps it would be worth getting it back in the queue to be fixed.  
  
@jzmaddock offered some feedback on the original ticket explaining the "all the digits you've got" meaning.  However, it seems to me that that meaning can be retained for `scientific` and `hexfloat` ouput, while `fixed` and `defaultfloat` should mimic whatever the standard library does with doubles.

---

## Comment 1

> Username: NAThompson  
> Created at: 2022-01-20 20:07:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/416#issuecomment-1017877223  

Isn't this a round-tripping issue? I think if you print it as a hexfloat it might how why that `..000030` is printed.

---

## Comment 2

> Username: cffk  
> Created at: 2022-01-20 20:12:09 UTC  
> Updated at: 2022-01-20 20:16:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/416#issuecomment-1017881049  

> Isn't this a round-tripping issue? I think if you print it as a hexfloat it might how why that `..000030` is printed.  
  
I'm not sure you understood what I'm looking for...  
  
I want a way to print a `float128` rounded to the nearest integer. `fixed << setprecision(0)` works for `double`s and I had hoped that it would work for `float128` --- but it doesn't!  
  
(I don't so much mind the `...000030` in the last line of output.  I do mind the `.1234`.)

---

## Comment 3

> Username: NAThompson  
> Created at: 2022-01-20 20:23:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/416#issuecomment-1017897536  

@cffk : Yup, sorry I misread your issue.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2022-01-24 11:50:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/416#issuecomment-1020016392  

Yup, duplicate of https://github.com/boostorg/multiprecision/issues/388.  
  
There is an open PR to address this (https://github.com/boostorg/multiprecision/pull/389), but I just need to double-check it and write some tests...
