# #371 - [Not an Issue] Fast nth fibonacci numbers [Closed]

> Username: madhur4127  
> Created at: 2020-06-14 16:47:03 UTC  
> Updated at: 2023-04-06 12:45:06 UTC  
> Closed at: 2023-04-06 12:45:06 UTC  
> Url: https://github.com/boostorg/math/issues/371  

A while back, I wrote a routine for [fast nth fibonacci number](https://github.com/CodeISM/ccptl/blob/master/Number%20Theory/fibonacci.h) calculation.  
  
Is boost/math interested in adding this to the library?  
  
I could add the functionality if the library's interested.

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-06-14 16:48:45 UTC  
> Url: https://github.com/boostorg/math/issues/371#issuecomment-643792282  

Should it go in boost.integer?

---

## Comment 2

> Username: madhur4127  
> Created at: 2020-06-14 16:55:56 UTC  
> Url: https://github.com/boostorg/math/issues/371#issuecomment-643793147  

> Should it go in boost.integer?  
  
The code that is linked only works it for *standard* C++ integers but I think it can be extended to work with arbitrary precision.

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-06-14 17:01:33 UTC  
> Updated at: 2020-06-14 17:20:56 UTC  
> Url: https://github.com/boostorg/math/issues/371#issuecomment-643793795  

I personally think it's a cool addition.  
  
The Fibonacci numbers grow so fast it might be nice to allow for arbitrary sized integers.  
  
Also, is there a fast way to detect if it's going to overflow? It might be nice to (say) return -1 if we know it'll overflow.

---

## Comment 4

> Username: madhur4127  
> Created at: 2020-06-14 17:32:19 UTC  
> Url: https://github.com/boostorg/math/issues/371#issuecomment-643797393  

> Also, is there a fast way to detect if it's going to overflow? It might be nice to (say) return -1 if we know it'll overflow.  
  
Binet formula can be used for a rough approximation, so the problem will reduce to whether the given number fits in the limit.  
  
But this will be a problem if someone tries to use a modular integer wrapper (considering that it takes the "integer" type as a template parameter) and only needs the modulus of the Fibonacci number. In this case, it will never overflow.

---

## Comment 5

> Username: NAThompson  
> Created at: 2020-06-15 15:10:27 UTC  
> Updated at: 2020-06-15 15:10:37 UTC  
> Url: https://github.com/boostorg/math/issues/371#issuecomment-644194291  

Can you do compile-time detection of whether it's a modular type?

---

## Comment 6

> Username: madhur4127  
> Created at: 2020-06-16 12:55:16 UTC  
> Url: https://github.com/boostorg/math/issues/371#issuecomment-644744381  

> Can you do compile-time detection of whether it's a modular type?  
  
I am out of ideas for that :(

---

## Comment 7

> Username: jzmaddock  
> Created at: 2020-06-16 14:44:39 UTC  
> Url: https://github.com/boostorg/math/issues/371#issuecomment-644811231  

>Can you do compile-time detection of whether it's a modular type?  
  
That's tough: we don't have modular type yet in multiprecision, so we don't have our own trait, and numeric_limits doesn't really give us the option of distinguishing between fixed precision integers that handle overflow as 2^n modular arithmetic, and "deliberately" modular types that are modulo some number that perhaps isn't a 2^n.

---

## Comment 8

> Username: NAThompson  
> Created at: 2020-06-16 14:56:05 UTC  
> Url: https://github.com/boostorg/math/issues/371#issuecomment-644818152  

Ok, cool. Just to clarify, I think it's a cool idea whether or not we can take care of this inconvenience without heroics.
