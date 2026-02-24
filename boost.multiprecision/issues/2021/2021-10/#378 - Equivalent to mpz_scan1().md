# #378 - Equivalent to mpz_scan1() [Closed]

> Username: afabri  
> Created at: 2021-10-07 12:41:01 UTC  
> Updated at: 2021-10-18 22:13:33 UTC  
> Closed at: 2021-10-07 12:49:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/378  

Is there a high level function to find the index of the first non zero bit like  [ mpz_scan1()](https://gmplib.org/manual/Integer-Logic-and-Bit-Fiddling) ?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-10-07 12:49:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/378#issuecomment-937760201  

msb and lsb (most/least significant bit) will do that.

---

## Comment 2

> Username: afabri  
> Created at: 2021-10-07 15:00:53 UTC  
> Updated at: 2021-10-07 15:41:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/378#issuecomment-937878239  

Modulo the fact that msb and lsb do not work with negative numbers.  Writing `lsb(abs(z))` seems not that great as it will make a copy of the limbs for nothing.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-10-07 17:36:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/378#issuecomment-938009824  

That's deliberate, since we wrap several different implementations there is no guarantee that the number is a signed-magnitude representation.  The functions also work with native integer types where that definitely isn't the case, and "msb" has no real meaning for a negative number.  If you need a gmp specific solution then:  
  
```  
unsigned scan1(const mpz_int& val) { return mpz_scan1(val.backend().data()); }  
```  
  
would do it.

---

## Comment 4

> Username: afabri  
> Created at: 2021-10-11 12:11:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/378#issuecomment-939973004  

I do not really get the argument.  Does `lsb()`  not also give us the least significant bit in a canonical representation and not in the implemented representation?

---

## Comment 5

> Username: jzmaddock  
> Created at: 2021-10-11 12:31:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/378#issuecomment-939987043  

>I do not really get the argument. Does lsb() not also give us the least significant bit in a canonical representation and not in the implemented representation?  
  
lsb also refuses to work on negative values.  I'm not sure that a "canonical representation" exists: IMO you could make a good case either way for that being 2's complement or signed-magnitude.

---

## Comment 6

> Username: afabri  
> Created at: 2021-10-18 15:44:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/378#issuecomment-945910971  

But what about `bit_test()` then.  Do  I get a true that depends on the representation?

---

## Comment 7

> Username: mglisse  
> Created at: 2021-10-18 22:13:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/378#issuecomment-946209962  

What options could there be for someone who wants an efficient `lsb(abs(x))`, and mostly just cares about `cpp_int` and `mpz_int`?  
  
Could we add an optional `eval_abs_lsb` (similar to `eval_multiply_add`) in backends, that would get called automatically (if it is provided) for an expression like `lsb(abs(x))`? There are many combinations with abs that could be optimized, even `a+abs(b)` could easily be done without copying b by some backend, so the question may become which ones, if any, are worth the trouble. It doesn't look like there is a general mechanism for backends to say "here is my optimized implementation of this weird combination of operations", each one requires an extra function, so we can't define too many.  
  
(if the number is not const and not readable from other threads, we can `bool b=sign(z)<0; if(b) z=-z; auto l = lsb(z); if(b) z=-z;` which I assume doesn't copy anything)  
  
I was also wondering about the possibility for some types to have some kind of `abs_view` (and `negate_view`) that looks like `abs` but returns a non-owning object that shares the buffer of its source. However, supporting that looks like it might require a lot of code changes...
