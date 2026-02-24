# #617 - conversion of big int to string [Open]

> Username: afabri  
> Created at: 2024-05-01 19:27:34 UTC  
> Updated at: 2024-05-30 11:25:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/617  

We use   `i.convert_to<std::string>`  for a multi precision integer `i` and observe a factor of 10 difference for the `cpp_int` backend compared to the `gmp_int` backend.    Is there a better way than using `convert_to()` ?

---

## Comment 1

> Username: NAThompson  
> Created at: 2024-05-01 20:18:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/617#issuecomment-2089068566  

@afabri : Could you post a reproducer? Sounds like a bug that needs fixed instead of something that needs a workaround.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2024-05-02 08:34:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/617#issuecomment-2089902041  

Indeed, plus is there any particular reason not to just call the `.str()` method?

---

## Comment 3

> Username: afabri  
> Created at: 2024-05-02 09:02:44 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/617#issuecomment-2089951338  

I have put a self contained program [here](https://gist.github.com/afabri/db497cd574b959c110f0a6adbfefb9f7)  on gist.github.com.    Calling `.str()`  has the same difference in performance.

---

## Comment 4

> Username: afabri  
> Created at: 2024-05-02 10:57:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/617#issuecomment-2090207095  

Under vtune I can see that it is the `divide_unsigned_helper()` which is time consuming.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2024-05-02 11:26:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/617#issuecomment-2090262100  

Ah performance, not value of result, I misunderstood, apologies.  
  
Yes we know that cpp_int division is terribly slow compared to GMP, but GMP is a tour-de-force of optimisation and we're not really trying to compete on that front (which is not to say we couldn't do better).

---

## Comment 6

> Username: afabri  
> Created at: 2024-05-02 11:44:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/617#issuecomment-2090298452  

In our use case (a multiprecision float with mantissa and exponent being arbitrary precision ints)  we are only interested in the leading N characters of the string representing the number.

---

## Comment 7

> Username: ckormanyos  
> Created at: 2024-05-04 06:43:38 UTC  
> Updated at: 2024-05-04 06:44:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/617#issuecomment-2094051359  

> In our use case (a multiprecision float with mantissa and exponent being arbitrary precision ints) we are only interested in the leading N characters of the string representing the number.  
  
Cound you do something a bit non-elegant like get the MSB (for $2^n$), then get the highest order limb and subsequently perform a table-based addition for the leading bits? It does not really sound like a lot of fun, but might work?

---

## Comment 8

> Username: afabri  
> Created at: 2024-05-29 09:19:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/617#issuecomment-2136944410  

> Ah performance, not value of result, I misunderstood, apologies.  
>   
> Yes we know that cpp_int division is terribly slow compared to GMP, but GMP is a tour-de-force of optimisation and we're not really trying to compete on that front (which is not to say we couldn't do better).  
  
@jzmaddock Can you give me a pointer to a publication that tells what has to be implemented to catch up?

---

## Comment 9

> Username: ckormanyos  
> Created at: 2024-05-30 11:23:03 UTC  
> Updated at: 2024-05-30 11:25:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/617#issuecomment-2139341899  

> to a publication that tells what has to be implemented to catch up?  
  
I'll put out a few details. `cpp_int` division is known to be slow. What could be done?  
  
  - Investigate Knuth long-division. I have an implementation of it (but with limbs ordered the other way around). Knuth long-division is quite difficult to program but it is rather quick for low limb counts.  
  - One of the things that can be done, but we are probably a long way away from, would be to visualize integer division as floating-point multiplication via numerator scaling-up and subsequent Newton-Raphson iteration and re-rationalization. If you convert `cpp_int` to `cpp_bin_float` this would be a very roundabout way to start seeing if this could help. Why? Besauce at least we go up to Karatsuba in multiplication. So although using Newton iteration to perform division is about as slow as 3 multiplications, you'd ultimately get sub-Order-N-squared in division.  
  
@jzmaddock might add more.  
  
P.S. I like the algorithm descriptions in:  
  
[1] R. Brent and P. Zimmermann, "Modern Computer Arithmetic", Cambridge University Press, 2011.  
[2] J. Arndt, "Matters Computational", Springer Verlag, 2011.  
[3] and of course the Knuth's volume 2.
