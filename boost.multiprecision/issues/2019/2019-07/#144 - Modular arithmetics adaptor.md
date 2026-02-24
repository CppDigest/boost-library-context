# #144 - Modular arithmetics adaptor [Open]

> Username: nemothenoone  
> Created at: 2019-07-16 12:05:21 UTC  
> Updated at: 2020-06-20 05:46:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/144  

For several purposes it would be great for the Boost.Multiprecision to have a modular arithmetics adaptor.  
  
Does it have any chance to be merged?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-07-16 12:25:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/144#issuecomment-511794078  

I'm not sure I follow what you mean, anything like: https://github.com/boostorg/multiprecision/blob/develop/performance/arithmetic_backend.hpp ?

---

## Comment 2

> Username: nemothenoone  
> Created at: 2019-07-16 12:32:59 UTC  
> Updated at: 2019-07-16 12:38:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/144#issuecomment-511796309  

Not really. It is actually about being able to do something like this:  
  
```  
typedef number<modular_adaptor<mpz_int > > modular_type  
modular_type a = {1, 4}, b = {3, 4}, c = a + b; // Implying c = 1 + 3 % 4 = 0;  
```  
  
or...  
  
```  
typedef number<modular_adaptor<cpp_int > > modular_type  
modular_type a = {1, 7}, b = {3, 7}, c = a + b; // Implying c = 1 + 3 % 7 = 4;  
```  
  
And this is achieved with implementation of something similar to ```complex_adaptor```.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-07-19 09:16:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/144#issuecomment-513157839  

Ah, sorry, stupid me!  
  
Yes, that's certainly something that was on the original TODO list but has been pushed back and back, but now that we've sorted out complex numbers, I guess this is the next number type to tackle.  It's probably not easy though - both the the algorithmic sense, and in getting the supporting architecture right.  
  
There's also an initialization problem - all the main algorithms for modular reduction require some precomputed data based on the modulus (which is what makes them fast), I guess ideally the modulus would be a template parameter and constexpr arithmetic would be used for that, but that's not going to be possible for mpz_int for example (even in C++20?), so you might be looking at defining a separate type for the modulus, and passing that type to the constructor:  
  
```  
   modulus<mpz_int> modulus(4);  
   modular_type a{1,modulus}, b{3, modulus};  
```  
  
and so on.  
  
I'm not especially keen on the syntax, but can't think of anything better off hand.  
  
Which modular reduction algorithms were you thinking of?

---

## Comment 4

> Username: nemothenoone  
> Created at: 2019-07-31 17:05:03 UTC  
> Updated at: 2019-07-31 17:21:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/144#issuecomment-516936176  

You are perfectly right with such a consideration, this is all was though about and partially done in ```2-```-prefixed branch in https://github.com/NilFoundation/multiprecision. For now I'm splitting all the stuff in that branch to separate branches and all of that (and more) will be proposed to Boost.Multiprecision.  
  
First of all several reduction techniques are being presented. Barrett reduction along with Montgomery reduction and newly introduced scheme from https://eprint.iacr.org/2014/040.pdf.  
  
As for module type intended for some precomputed params storage, it is also was done pretty similar to your suggestion. The only difference - no ```constexpr``` computations were assumed to be used, but this is a great idea. I do not, actually, see ```constexpr``` computations coming until #84 gets resolved. So now I'm looking at ctbignum library, how it was done in there, if I could maybe port something from there to Boost.Multiprecision.  
  
Definitely no ```constexpr``` computation of such params would be done exactly for ```gmp_int``` or any other plain-C library backend wrappers, but this could be replaced with compile-time computation of such values with constexpr-based ```cpp_int``` and then exporting the result to the type used in particular application (e.g. ```gmp_int```). So the only runtime overhead would be exporting to particular number type, all the other precomputation stuff will be done in compile-time.  
  
Such a separation of a monster branch to feature branches (I mentioned it above) actually depends on #146, #143 and #142 because this is not a hotfix of simple patch, this requires complex changes which could be only done in consensus with the upstream maintainer (I don't want to merge something in my own ```develop``` branch in case this would never be in the upstream). PRs mentioned have to be resolved in some way so I could establish the development process being sure about something would be upstreamed or it will never be, so I could simply fork the library.

---

## Comment 5

> Username: nemothenoone  
> Created at: 2020-06-20 05:46:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/144#issuecomment-646946285  

I believe it would be great to link this issue to PR #161, so it will close just as that PR will be merged.
