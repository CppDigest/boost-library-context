# #537 - Speed of division with remainder for cpp_int [Open]

> Username: afabri  
> Created at: 2023-02-24 13:18:54 UTC  
> Updated at: 2023-02-27 12:33:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/537  

In a geometric code I observe a slowdown of a factor 4, when I switch from the gmp to the cpp_int backend.  In vtune I see that more than 35% of the time are spent [here](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/cpp_int/divide.hpp#L231).    The [comment](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/cpp_int/divide.hpp#L48) at the signature leads me to the question if I am in the scenario where this function is not optimized.   Is there a trivial fix? The comment seems to indicate that it is optimized for small numbers of limbs.

---

## Comment 1

> Username: afabri  
> Created at: 2023-02-24 14:36:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/537#issuecomment-1443766642  

In my case the numerator has 78000 digits and the denominator 30000.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2023-02-24 18:10:34 UTC  
> Updated at: 2023-02-24 18:11:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/537#issuecomment-1444172446  

It might be possible to _low-word_ the shift on the carry. But I am really not sure if that exact sequence is using full multiples of the number of bits in a limb for the shift.  
  
As a general comment, it's really hard to beat or match the raw speed of GMP when using portable code. They squeeze the assembler for their well-deserved, legendary reputation for bare-metal speed.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2023-02-24 18:12:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/537#issuecomment-1444176433  

I'm not surprised by this.  Division is hard, so many corner cases to handle correctly.  There are better algorithms (in the big-O sense), but it's a significant piece of work to implement them correctly.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2023-02-24 18:47:27 UTC  
> Updated at: 2023-02-24 19:02:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/537#issuecomment-1444247014  

>  Division is hard, so many corner cases to handle correctly  
  
I actually have a Knuth long division in an unrelated project. I've run hundreds of millions of tests on it and squeezed all the bugs out over the course of, my gosh, years. I use an opposite limb-ordering.  
  
I feel that Knuth long division is the winner for limb-counts below 2,000 or so. But at that range of your test case, you're in the quadratic domain of classic algorithms. You'd probably be better off inverting-and-multiplying with conversion to bin-float, then Newton-Raphson-ing it. But to assemble the result, you'd be desperately driven to make sure you rorund correctly. Oh my gosh, I get exahusted just thingking about verifying and testing that code for hard-world-open use.  
  
I wouldn't take on either of these tasks at the moment. But maybe note this as a potential place for optimization in the future...  
As these are really daunting tasks.

---

## Comment 5

> Username: ckormanyos  
> Created at: 2023-02-24 18:53:38 UTC  
> Updated at: 2023-02-24 18:55:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/537#issuecomment-1444257504  

> actually have a Knuth long division  
  
Since I've never spoken to, really anyone about this one and it's a gem, ...  
  
Here is that [Knuth long division](https://github.com/ckormanyos/wide-integer/blob/37a9bdaf3d3c0894134141f2893164dc80fb0c79/math/wide_integer/uintwide_t.h#L4195) - in it's full-on, loving, 300 lines of glory (albeit with the wrong limb-ordering) for future reference if anyone ever cares for it.
