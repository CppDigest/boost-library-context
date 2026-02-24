# #40 - PCG Generator [Open]

> Username: NAThompson  
> Created at: 2018-07-03 23:27:14 UTC  
> Updated at: 2019-07-05 12:15:24 UTC  
> Url: https://github.com/boostorg/random/issues/40  

There has been a lot of complaining on the internet recently about how `<random>` has poor random number generators. I am wondering if there is interest in implementation and addition of the PCG random number generator to boost.random, with an eye towards addition into `<random>`. The implementation does not look difficult.  
  
http://www.pcg-random.org/download.html

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-07-04 00:06:01 UTC  
> Url: https://github.com/boostorg/random/issues/40#issuecomment-402325134  

AMDG  
  
On 07/03/2018 05:27 PM, Nick wrote:  
> There has been a lot of complaining on the internet recently about how `<random>` has poor random number generators. I am wondering if there is interest in implementation and addition of the PCG random number generator to boost.random, with an eye towards addition into `<random>`. The implementation does not look difficult.  
>   
> http://www.pcg-random.org/download.html  
>   
  
I will accept it if it's submitted, but I'll probably  
never get around to implementing it myself.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: dhardy  
> Created at: 2019-07-03 09:35:56 UTC  
> Url: https://github.com/boostorg/random/issues/40#issuecomment-508019643  

FYI, the author's C++ implementation is here (https://github.com/imneme/pcg-cpp), but this includes *many* RNG variants. For the [Rust random library](https://github.com/rust-random/rand/tree/master/rand_pcg) I choose to include only three variants (`pcg32`, `pcg64` and `pcg64_fast`) which requires much less code and avoids the need for most of the generics.  
  
Worth noting is there is a minor difference between the C and C++ implementations of MCG variants (see [here](https://github.com/imneme/pcg-cpp/issues/51#issuecomment-506783189)).

---

## Comment 3

> Username: kotika  
> Created at: 2019-07-05 11:46:53 UTC  
> Url: https://github.com/boostorg/random/issues/40#issuecomment-508732772  

> On Jul 3, 2019, at 12:35, Diggory Hardy <notifications@github.com> wrote:  
> FYI, the author's C++ implementation is here (https://github.com/imneme/pcg-cpp), but this includes many RNG variants. For the Rust random library I choose to include only three variants (pcg32, pcg64 and pcg64_fast) which requires much less code and avoids the need for most of the generics.  
>   
I would caution you against rejecting the tried and true generators in boost, to which my own MIXMAX RNG is hopefully coming soon and going with  
an unpublished and unpublishable invention. Looking at their own page, they claim   
  
Period: Arbitrary					  
k-Dimensional Equidistribution: Arbitrary*  
  
Further down the page, we see   
"The PCG family uses a linear congruential generator as the state-transition function"  
  
These 32 and 64 bit  LCGs which you are planning to use, just as any other LCGs,  are not capable of period greater than 2^bits-of-state - too small,   
and equidistribution in double-precision can be at most k=1, it will not even be strictly uniform in k=2.  
  
Then there is the idea, that in an LCG, x'=ax+c, the variable c parameter can be used to produce independent streams, that got to be a product of total  
misunderstanding of the theory.  
  
There is a variety other amateurish musings in that paper, giving away the author as a non-expert....no wander it was rejected by ACM.  
  
Kostas Savvidis, PhD  
  
============================================================================================  
Institute of Nuclear and Particle Physics  
NCSR Demokritos  
https://github.com/kotika/random  
https://mixmax.hepforge.org

---

## Comment 4

> Username: dhardy  
> Created at: 2019-07-05 12:04:54 UTC  
> Url: https://github.com/boostorg/random/issues/40#issuecomment-508737033  

And the only item of any substance in your post concerns the period. But you are also incorrect: `pcg32` is in fact a 64-bit LCG, and `pcg64` is a *128-bit* LCG. These are combined with a transition function and only output half the bits at a time, hence the names.

---

## Comment 5

> Username: kotika  
> Created at: 2019-07-05 12:15:24 UTC  
> Url: https://github.com/boostorg/random/issues/40#issuecomment-508739576  

> On Jul 5, 2019, at 15:04, Diggory Hardy <notifications@github.com> wrote:  
>   
> And the only item of any substance in your post concerns the period. But you are also incorrect: pcg32 is in fact a 64-bit LCG, and pcg64 is a 128-bit LCG. These are combined with a transition function and only output half the bits at a time, hence the names.  
>   
Yes, sorry, you are right, PCG32 is the 64-bit LCG from the Knuth TAOCP vol.2, page 106, table 1, line 26,  
plus its got an "output" function, vaguely lifted out of MersenneTwister. It is not a bad LCG, and better than any 32bit LCG, so   
its OK to use it for maybe a billion random numbers. If you need more, then my previous comments stand.  
  
KS  
uint32_t pcg32_random_r(pcg32_random_t* rng)  
 <>{  
 <>    uint64_t oldstate = rng->state;  
 <>    // Advance internal state  
 <>    rng->state = oldstate * 6364136223846793005ULL + (rng->inc|1);  
 <>    // Calculate output function (XSH RR), uses old state for max ILP  
 <>    uint32_t xorshifted = ((oldstate >> 18u) ^ oldstate) >> 27u;  
 <>    uint32_t rot = oldstate >> 59u;  
 <>    return (xorshifted >> rot) | (xorshifted << ((-rot) & 31));  
 <>}
