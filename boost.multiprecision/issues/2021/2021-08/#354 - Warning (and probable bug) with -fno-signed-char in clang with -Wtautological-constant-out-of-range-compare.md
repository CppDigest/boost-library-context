# #354 - Warning (and probable bug) with -fno-signed-char in clang with -Wtautological-constant-out-of-range-compare [Closed]

> Username: gordondow  
> Created at: 2021-08-10 01:49:34 UTC  
> Updated at: 2021-08-11 18:14:00 UTC  
> Closed at: 2021-08-11 18:12:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/354  

If I enable the -fno-signed-char flag and build Boost.Multiprecision with clang and `-Wtautological-constant-out-of-range-compare` enabled, I get the following warning in `boost/multiprecision/number.hpp`:  
  
```  
error: result of comparison of constant -1 with expression of type 'char' is always true [-Werror,-Wtautological-constant-out-of-range-compare]  
   while ((EOF != (c = static_cast<char>(is.peek()))) && (c == 'x' || c == 'X' || c == '-' || c == '+' || (c >= '0' && c <= '9') || (have_hex && (c >= 'a' && c <= 'f')) || (have_hex && (c >= 'A' && c <= 'F'))))  
```  
  
(An identical error appears a few lines later) I think this is a real issue -- if `peek()` returns EOF and char is unsigned, it will get turned into 0xFF and will not compare-equal to EOF.  
  
I _think_ this can be fixed simply by changing `c`'s type to `signed char` in this function. I don't know if there are other similar issues lurking elsewhere in the code.  
  
This is an issue as Google has declared that in the latest Android NDK one must not use their STL with -fsigned-char.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-08-11 18:08:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/354#issuecomment-897039626  

Confirmed.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-08-11 18:14:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/354#issuecomment-897043933  

Fixed in https://github.com/boostorg/multiprecision/pull/353
