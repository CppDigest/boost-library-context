# #790 - Wrong value in max_number_chars? [Open]

> Username: vinniefalco  
> Created at: 2022-10-26 23:52:36 UTC  
> Updated at: 2023-02-03 13:16:53 UTC  
> Url: https://github.com/boostorg/json/issues/790  

Shouldn't 19 be 20 here?  
https://github.com/boostorg/json/blob/develop/include/boost/json/detail/format.hpp#L18  
  
because 2^64-1 = 18,446,744,073,709,551,615 which is 20 digits not 19

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-02-03 13:16:52 UTC  
> Url: https://github.com/boostorg/json/issues/790#issuecomment-1415859017  

Looking at our embedded sources of ryu I discovered that it only allows 17 decimal digits in mantissa (and even asserts on it). After some research I've confirmed that 17 is the largest number of decimal digits that IEEE 754 double supports. The number 19 that was used in the constant seems to have originated from the maximum number of decimal digits that an unsigned 64 bit integer may require. But since the constant also takes into account characters necessary for exponent and the decimal point, it could be disregarded. So, the correct value there should be 17.  
  
I checked, and the tests pass. The difference is probably irrelevant, though, because the buffer fits into 32 bits either way.
