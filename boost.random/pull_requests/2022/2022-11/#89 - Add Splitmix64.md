# #89 Add Splitmix64 [Merged]

> Username: mborland  
> Created at: 2022-11-27 20:28:03 UTC  
> Updated at: 2023-12-15 07:45:56 UTC  
> Merged at: 2023-12-15 07:45:47 UTC  
> Closed at: 2023-12-15 07:45:47 UTC  
> Url: https://github.com/boostorg/random/pull/89  

Adds the Splitmix64 generator from Sebastiano Vigna as described [here](https://dl.acm.org/doi/10.1145/2714064.2660195). Will be used for the future implementation (generates the initial seed) of the [xoroshiro family of PRNGs](https://prng.di.unimi.it). The performance comparison of the STL and current Boost.Random random to Splitmix64 is shown below:  
  
```  
Running ./random_device  
Run on (10 X 24.1211 MHz CPU s)  
CPU Caches:  
  L1 Data 64 KiB  
  L1 Instruction 128 KiB  
  L2 Unified 4096 KiB (x10)  
Load Average: 1.41, 1.62, 1.84  
--------------------------------------------------------------------------  
Benchmark                                Time             CPU   Iterations  
--------------------------------------------------------------------------  
stl_random_device<unsigned>            499 ns          498 ns      1039378  
boost_random_device<unsigned>          500 ns          500 ns      1399804  
boost_splitmix64<std::uint64_t>       5.23 ns         5.23 ns    133605634  
```

---

## Comment 1

> Username: mborland  
> Created_at: 2022-11-27 20:29:13 UTC  
> Url: https://github.com/boostorg/random/pull/89#issuecomment-1328333438  

@NAThompson I assume you will have some interest in this since you have asked for other PRNGs before.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2022-11-27 20:58:33 UTC  
> Url: https://github.com/boostorg/random/pull/89#issuecomment-1328338308  

@mborland : std::random_device is a hardware TRNG, whereas splitmix64 is a PRNG right? Shouldn't the comparison be to (say) PCG32?

---

## Review 3 [Commented]

> Username: NAThompson  
> Created_at: 2022-11-27 21:00:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/random/pull/89#pullrequestreview-1195054533  

📁 include/boost/random/splitmix64.hpp

```diff
   1 |+ /* 
   2 |+  * Copyright Sebastiano Vigna 2015.
```

> Username: NAThompson  
> Created_at: 2022-11-27 21:00:18 UTC  
> Updated_at: 2022-11-27 21:00:19 UTC  
> Url: https://github.com/boostorg/random/pull/89#discussion_r1033008927  

Did Sebastiano Vigna write this code?

> Username: mborland  
> Created_at: 2022-11-28 00:37:58 UTC  
> Url: https://github.com/boostorg/random/pull/89#discussion_r1033041659  

The implementation of `next` is almost identical to his published C code. His code is all licensed under the Creative Commons license which is compatible with the Boost Software License.

> Username: jzmaddock  
> Created_at: 2022-11-29 12:18:13 UTC  
> Url: https://github.com/boostorg/random/pull/89#discussion_r1034678090  

Just double checking this as creative commons isn't always BSL compatible, but there's an explicitly "public domain" version so this really is OK ;)


---

## Review 4 [Commented]

> Username: NAThompson  
> Created_at: 2022-11-27 21:01:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/random/pull/89#pullrequestreview-1195054625  

📁 performance/random_device.cpp

```diff
  38 |+     for (auto _ : state)
  39 |+     {
  40 |+         benchmark::DoNotOptimize(rd());
```

> Username: NAThompson  
> Created_at: 2022-11-27 21:01:08 UTC  
> Url: https://github.com/boostorg/random/pull/89#discussion_r1033009048  

For PRNGs, it is often necessary to do multiple calls to get accurate performance numbers because the loop overhead is so high.


---

## Review 5 [Commented]

> Username: NAThompson  
> Created_at: 2022-11-27 21:01:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/random/pull/89#pullrequestreview-1195054696  

📁 test/test_splitmix64.cpp

```diff
  21 |+     boost::random::splitmix64 rng;
  22 |+     
  23 |+     double entropy = rng.entropy();
```

> Username: NAThompson  
> Created_at: 2022-11-27 21:01:50 UTC  
> Url: https://github.com/boostorg/random/pull/89#discussion_r1033009127  

I confess to not understanding the `.entropy()` member here; is this the entropy of a uniform distribution?

> Username: mborland  
> Created_at: 2022-11-28 00:29:48 UTC  
> Url: https://github.com/boostorg/random/pull/89#discussion_r1033040203  

Since this is a [PRNG](https://en.cppreference.com/w/cpp/numeric/random/random_device/entropy) it is always 0 so not overly useful. Removed.


---

## Comment 6

> Username: mborland  
> Created_at: 2022-11-28 00:39:03 UTC  
> Url: https://github.com/boostorg/random/pull/89#issuecomment-1328387785  

> @mborland : std::random_device is a hardware TRNG, whereas splitmix64 is a PRNG right? Shouldn't the comparison be to (say) PCG32?  
  
You are correct. Here is a more accurate comparison of 64-bit PRNGs:  
  
```  
Running ./random_device  
Run on (10 X 24.0415 MHz CPU s)  
CPU Caches:  
  L1 Data 64 KiB  
  L1 Instruction 128 KiB  
  L2 Unified 4096 KiB (x10)  
Load Average: 1.72, 2.10, 2.47  
--------------------------------------------------------------------------  
Benchmark                                Time             CPU   Iterations  
--------------------------------------------------------------------------  
stl_mt19937_64<std::uint64_t>          128 ns          128 ns      5110757  
boost_mt19937_64<std::uint64_t>       72.0 ns         72.0 ns      9697574  
boost_splitmix64<std::uint64_t>       38.2 ns         38.2 ns     18326430  
```

---

## Comment 7

> Username: NAThompson  
> Created_at: 2022-11-28 03:50:36 UTC  
> Updated_at: 2022-11-28 03:54:37 UTC  
> Url: https://github.com/boostorg/random/pull/89#issuecomment-1328499952  

@mborland : For some reason these numbers seem unreasonably slow. I expect `mt19937_64` to execute in about 5 ns . . .  
  
Edit: Sorry for the noise; I see that you call it 10x which is about right.

---

## Review 8 [Commented]

> Username: NAThompson  
> Created_at: 2022-11-28 03:52:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/random/pull/89#pullrequestreview-1195176527  

📁 include/boost/random/splitmix64.hpp

```diff
  33 |+     using result_type = std::uint64_t;
  34 |+     
  35 |+     splitmix64()
```

> Username: NAThompson  
> Created_at: 2022-11-28 03:52:01 UTC  
> Url: https://github.com/boostorg/random/pull/89#discussion_r1033105222  

What about adding `splitmix64(int64_t seed = 0)` where `seed==0` means "choose a TRNG seed"?


---

## Review 9 [Commented]

> Username: NAThompson  
> Created_at: 2022-11-28 03:53:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/random/pull/89#pullrequestreview-1195177671  

📁 test/test_splitmix64.cpp

```diff
  29 |+     std::array<std::uint64_t, 1000> test_array {};
  30 |+     rng.generate(test_array.begin(), test_array.end());
  31 |+ 
```

> Username: NAThompson  
> Created_at: 2022-11-28 03:53:47 UTC  
> Url: https://github.com/boostorg/random/pull/89#discussion_r1033105952  

Could you test that your implementation is compatible with various distributions? i.e., pipe it through std::normal_distribution and `std::uniform_distribution` and `std::gamma_distribution`?  
  
It's very easy for an integer RNG to not meet the conceptual requirements to map into a continuous distribution.


---

## Review 10 [Commented]

> Username: NAThompson  
> Created_at: 2022-11-28 03:54:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/random/pull/89#pullrequestreview-1195178108  

📁 include/boost/random/splitmix64.hpp

```diff
  49 |+     splitmix64 operator=(const splitmix64&) = delete;
  50 |+ 
  51 |+     std::uint64_t next() noexcept
```

> Username: NAThompson  
> Created_at: 2022-11-28 03:54:49 UTC  
> Url: https://github.com/boostorg/random/pull/89#discussion_r1033106310  

inline?

> Username: mborland  
> Created_at: 2022-11-28 21:54:46 UTC  
> Url: https://github.com/boostorg/random/pull/89#discussion_r1034091041  

Inline is implicit for in-class definition, but does not hurt to make it explicit.


---

## Review 11 [Commented]

> Username: NAThompson  
> Created_at: 2022-11-28 03:54:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/random/pull/89#pullrequestreview-1195178189  

📁 include/boost/random/splitmix64.hpp

```diff
  58 |+     }
  59 |+ 
  60 |+     std::uint64_t operator()() noexcept
```

> Username: NAThompson  
> Created_at: 2022-11-28 03:54:59 UTC  
> Url: https://github.com/boostorg/random/pull/89#discussion_r1033106364  

inline?


---

## Review 12 [Commented]

> Username: NAThompson  
> Created_at: 2022-11-28 03:55:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/random/pull/89#pullrequestreview-1195178549  

📁 include/boost/random/splitmix64.hpp

```diff
  74 |+ 
  75 |+     template <typename FIter>
  76 |+     void generate(FIter first, FIter last)
```

> Username: NAThompson  
> Created_at: 2022-11-28 03:55:50 UTC  
> Updated_at: 2022-11-28 03:55:51 UTC  
> Url: https://github.com/boostorg/random/pull/89#discussion_r1033106636  

Is a `generate` member standard in C++ PRNGs? Or will it be in C++2x?

> Username: mborland  
> Created_at: 2022-11-28 21:56:24 UTC  
> Url: https://github.com/boostorg/random/pull/89#discussion_r1034092341  

It is not a standard member in the STL, but the Boost versions have this functionality so for the sake of consistency I added it.


---

## Comment 13

> Username: NAThompson  
> Created_at: 2022-11-28 03:57:56 UTC  
> Url: https://github.com/boostorg/random/pull/89#issuecomment-1328503056  

One unit test which might be useful: Generally for PRNGs, we have requirements like [this one](https://en.cppreference.com/w/cpp/numeric/random/mersenne_twister_engine):  
  
  
<h3><span class="mw-headline" id="Notes">Notes</span></h3>  
<p>The Nth consecutive invocation of a default-constructed engine is required to produce the following value:  
</p>  
  
  
N | The random engine type | The value to produce  
-- | -- | --  
10000 | std::mt19937 | 4123659995  
10000 | std::mt19937_64 | 9981545732273789042  
  
  
Is there an analog for splitmix64? Of course, you could introduce your own definition and simply document it.

---

## Comment 14

> Username: kotika  
> Created_at: 2022-11-28 16:15:25 UTC  
> Updated_at: 2022-12-01 09:55:26 UTC  
> Url: https://github.com/boostorg/random/pull/89#issuecomment-1329366614  

> On Nov 27, 2022, at 23:00, Nick ***@***.***> wrote:  
> @NAThompson commented on this pull request.  
>   
> In include/boost/random/splitmix64.hpp <https://github.com/boostorg/random/pull/89#discussion_r1033008927>:  
>   
> > @@ -0,0 +1,98 @@  
> +/*   
> + * Copyright Sebastiano Vigna 2015.  
> Did Sebastiano Vigna write this code?  
>   
  
Alert:  
  
1) you cannot safely use up more than a cubic root of the period of any generator. This generator's period is apparently less than 2^64 but its not stated how much exatly.  
A cubic root of that is ~2^21. Thus you must not use this generator in any program that will run longer than a millisecond.  
In particular, you would not use it to create large numbers of streams on a multiprocessor system.  
  
2) This generator is not from Vigna, the authors are not known to be experts on rngs, and the principle of construction   
 is obscure. It is neither an LCG, nor MRG, nor LFSR, nor counter based crypto. What is it exactly?   
  
3) For industrial strength RNG on boost, use MIXMAX. It is parallelizable and splittable:  
boost::random::mixmax gen{seed1,seed2,seed3,seed4};   
It is enough that at least one of the 32bit seeds is different by one bit. This gives you 2^128 guaranteed non-colliding streams that pass all tests.  
  
Cheers,  
Kostas  
  
=========================================  
Institute of Nuclear and Particle Physics  
NCSR Demokritos  
http://inspirehep.net/author/profile/K.G.Savvidy.1  
https://mixmax.hepforge.org

---

## Comment 15

> Username: mborland  
> Created_at: 2022-11-28 21:53:12 UTC  
> Url: https://github.com/boostorg/random/pull/89#issuecomment-1329804443  

Kostas,  
  
The primary use for this is to seed the xoshiro/xoroshiro family of generators as recommended by the authors [here](https://dl.acm.org/doi/10.1145/1276927.1276928). The original C code is copyrighted by Vigna and David Blackman for the generators. I think it would be disingenuous to say that are not experts given the [large amount of literature they have published on the subject.](https://vigna.di.unimi.it/papers.php) A [large number of languages](https://prng.di.unimi.it/#quality) use these as their standard PRNG so I think it would be useful if we offered them as well.

---

## Comment 16

> Username: swatanabe  
> Created_at: 2022-11-29 03:15:17 UTC  
> Url: https://github.com/boostorg/random/pull/89#issuecomment-1330020530  

On 11/28/22 14:56, Matt Borland wrote:  
> @mborland commented on this pull request.  
>   
> +    template <typename FIter>  
> +    void generate(FIter first, FIter last)  
>   
> It is not a standard member in the STL, but the Boost versions have this functionality so for the sake of consistency I added it.  
>   
  
The reason it exists is to make every generator usable as a SeedSeq,  
for compatibility with ancient (pre-C++11) Boost.Random.  It's not  
very relevant anymore.

---

## Comment 17

> Username: swatanabe  
> Created_at: 2022-11-29 03:33:23 UTC  
> Url: https://github.com/boostorg/random/pull/89#issuecomment-1330030157  

On 11/27/22 20:53, Nick wrote:  
> Could you test that your implementation is compatible with various distributions? i.e., pipe it through std::normal_distribution and `std::uniform_distribution` and `std::gamma_distribution`?  
>   
> It's very easy for an integer RNG to not meet the conceptual requirements to map into a continuous distribution.  
>   
  
Please do use the generic test harness for random engines. It has some  
obsolete functionality, like the iterator seed routines and the generate  
function that should probably not be supported for new engines and  
should be #ifdef'ed out in test_generator.ipp, but it will catch  
things like the missing seed_seq constructor and the missing  
seed function.  
  
Also, the implementation of generate is wrong. generate is supposed  
fill a range with 32-bit values, regardless of the both the engine's  
value_type and the array's element type.

---

## Comment 18

> Username: jzmaddock  
> Created_at: 2022-11-29 12:20:02 UTC  
> Url: https://github.com/boostorg/random/pull/89#issuecomment-1330544508  

>The primary use for this is to seed the xoshiro/xoroshiro family of generators as recommended by the authors [here](https://dl.acm.org/doi/10.1145/1276927.1276928). The original C code is copyrighted by Vigna and David Blackman for the generators. I think it would be disingenuous to say that are not experts given the [large amount of literature they have published on the subject.](https://vigna.di.unimi.it/papers.php) A [large number of languages](https://prng.di.unimi.it/#quality) use these as their standard PRNG so I think it would be useful if we offered them as well.  
  
+1, it's Java's default PRNG which is good enough reason to offer it as an option at least.  It's drawbacks should be well documented though.

---

## Comment 19

> Username: mborland  
> Created_at: 2022-11-30 16:36:59 UTC  
> Url: https://github.com/boostorg/random/pull/89#issuecomment-1332439792  

> On 11/27/22 20:53, Nick wrote: Could you test that your implementation is compatible with various distributions? i.e., pipe it through std::normal_distribution and `std::uniform_distribution` and `std::gamma_distribution`? It's very easy for an integer RNG to not meet the conceptual requirements to map into a continuous distribution.  
> Please do use the generic test harness for random engines. It has some obsolete functionality, like the iterator seed routines and the generate function that should probably not be supported for new engines and should be #ifdef'ed out in test_generator.ipp, but it will catch things like the missing seed_seq constructor and the missing seed function. Also, the implementation of generate is wrong. generate is supposed fill a range with 32-bit values, regardless of the both the engine's value_type and the array's element type.  
  
The generic test harness is used and now passes on CI. I removed two of the obsolete constructor tests you mentioned. I ended up leaving the generate function in otherwise I would have to remove all of the concepts tests. They seem valuable to retain.

---

## Comment 20

> Username: mborland  
> Created_at: 2022-11-30 16:39:20 UTC  
> Url: https://github.com/boostorg/random/pull/89#issuecomment-1332442691  

> On Nov 28, 2022, at 23:53, Matt Borland ***@***.***> wrote: Kostas, The primary use for this is to seed the xoshiro/xoroshiro family of generators as recommended by the authors here <https://dl.acm.org/doi/10.1145/1276927.1276928>. The original C code is copyrighted by Vigna and David Blackman for the generators. I think it would be disingenuous to say that are not experts given the large amount of literature they have published on the subject. <https://vigna.di.unimi.it/papers.php> A large number of languages <https://prng.di.unimi.it/#quality> use these as their standard PRNG so I think it would be useful if we offered them as well.  
> I am in favor of including the generator of Vigna, but the fact that some generator needs another "good" generator to seed it is evidence that it has long internal decorrelation time, like the Mersenne Twister. And in fact Vigna's generators do have this unfortunate property. Ideally any new generators should be constructed with short decorrelation times and seeded by skipping along, like my generator. It needs neither non-linear seeding, nor an output scrambling function. At a practical level, why is this being done ahead of or instead of including an implementation of Vignas generator? Cheers, Kostas ========================================= Institute of Nuclear and Particle Physics NCSR Demokritos http://inspirehep.net/author/profile/K.G.Savvidy.1 https://mixmax.hepforge.org  
  
I separated this from the implementation of the other generators for ease of review. This is over 300 lines, and to include it as an implementation detail of the generators seemed unwieldy to me.

---

## Comment 21

> Username: kotika  
> Created_at: 2022-11-30 19:03:27 UTC  
> Updated_at: 2022-12-01 09:57:21 UTC  
> Url: https://github.com/boostorg/random/pull/89#issuecomment-1332606251  

OK, I have a better idea now of what this thing is. Steele et al wrote a paper in 2014 suggesting to split random number streams by means of "pedigree". The pedigree idea is sound, but the realization was and is flawed. Same mistake was made by the authors of PCG, namely that you might create a new generator by changing the additive constant on the fly. Java even now ships with this flawed implementation of splitting, as far as i can tell. Since the state transition function is just uint64 state += c, so the period is indeed 2^64 for odd c.  
  
  Turns out not all constants are created equal and some lead to detectable flaws. Vigna wrote code based on a paper by Steele, and threw away the flawed idea of producing split streams by choosing a specific additive constant. Thats the "0x9E3779B97F4A7C15". He puts his copyright on --  justly so because he did not copy any code -- then you copy-paste and wrap it in boost, and put your own copyright onto it. Is this even kosher? The file has a Copyright at the top, even if lower down it has a Creative commons license. The license I suppose gives you the legal right to do so, but ....  
 shouldn't it at least say the algorithm is by Steele et al? Some of the many versions of splitmix64 floating around the internet have   
  
> Fast Splittable Pseudorandom Number Generators  
> Steele Jr, Guy L., Doug Lea, and Christine H. Flood. "Fast splittable  
> pseudorandom number generators."  
> ACM SIGPLAN Notices 49.10 (2014): 453-472.  
  
Much of Steele's subsequent work on random numbers is patented and assigned to Oracle, btw. I am not thrilled about that either.  
  
But is the algorithm really by Steele et al? It is  state += c, plus an output=hash(state), where the hash-like function was invented by yet another person, Austin Appleby for MurmurHash3, and David Stafford, who modified the constants in MurmurHash3, in a blog post, based on dubious criteria.  
  
On the contrary, Austin Appleby appears to be alive and well, developes his own original software which reads:  
https://github.com/aappleby/smhasher/blob/master/src/MurmurHash3.cpp  
  
> // MurmurHash3 was written by Austin Appleby, and is placed in the public  
> // domain. The author hereby disclaims copyright to this source code.  
>   
> uint64_t fmix64 ( uint64_t k )  
> {  
>   k ^= k >> 33;  
>   k *= BIG_CONSTANT(0xff51afd7ed558ccd);  
>   k ^= k >> 33;  
>   k *= BIG_CONSTANT(0xc4ceb9fe1a85ec53);  
>   k ^= k >> 33;  
>   
>   return k;  
> }

---

## Comment 22

> Username: mborland  
> Created_at: 2022-12-01 04:59:05 UTC  
> Url: https://github.com/boostorg/random/pull/89#issuecomment-1333171744  

@kotika There is already a link to the paper on ACM from Steele et. al. in the comments at the top of the file.  
  
@jzmaddock, @swatanabe, and @NAThompson I think this is good for review. This passes CI using the existing generic test harness and concept tests with tailoring for C++11 as recommended by Steve.

---

## Comment 23

> Username: NAThompson  
> Created_at: 2023-02-01 20:34:54 UTC  
> Url: https://github.com/boostorg/random/pull/89#issuecomment-1412687892  

@swatanabe : @mborland is now in the employ of boost. Would you mind giving him maintainer access in order that he could merge a few of his MRs?

---

## Comment 24

> Username: jzmaddock  
> Created_at: 2023-02-02 11:28:11 UTC  
> Url: https://github.com/boostorg/random/pull/89#issuecomment-1413587895  

Actually @swatanabe agreed to this off list, sorry been busy with other things, just give Matt write access now.

---

## Review 25 [Commented]

> Username: swatanabe  
> Created_at: 2023-05-13 18:01:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/random/pull/89#pullrequestreview-1425446132  

📁 include/boost/random/splitmix64.hpp

```diff
 129 |+                                                                const splitmix64& e)
 130 |+     {
 131 |+         std::string sstate = std::to_string(e.state_);
```

> Username: swatanabe  
> Created_at: 2023-05-13 17:52:17 UTC  
> Updated_at: 2023-05-13 18:01:25 UTC  
> Url: https://github.com/boostorg/random/pull/89#discussion_r1193019209  

This seems strange. What's wrong with `ost << e.state_`?

---

📁 include/boost/random/splitmix64.hpp

```diff
 142 |+                                                         splitmix64& e)
 143 |+     {
 144 |+         std::string sstate;
```

> Username: swatanabe  
> Created_at: 2023-05-13 17:55:11 UTC  
> Updated_at: 2023-05-13 18:01:25 UTC  
> Url: https://github.com/boostorg/random/pull/89#discussion_r1193019488  

This implementation is definitely wrong:  
- It allows whitespace in the middle of a single number which makes no sense.  
- It is likely to consume extra input characters silently

---

📁 include/boost/random/splitmix64.hpp

```diff
 112 |+     inline friend bool operator==(const splitmix64& lhs, const splitmix64& rhs) noexcept
 113 |+     {
 114 |+         if (lhs.state_ == rhs.state_)
```

> Username: swatanabe  
> Created_at: 2023-05-13 17:56:10 UTC  
> Updated_at: 2023-05-13 18:01:25 UTC  
> Url: https://github.com/boostorg/random/pull/89#discussion_r1193019582  

Just `return lhs.state_ == rhs.state_`. There's no need for a conditional.

---

📁 include/boost/random/splitmix64.hpp

```diff
  26 |+ #include <string>
  27 |+ #include <ios>
  28 |+ #include <iostream>
```

> Username: swatanabe  
> Created_at: 2023-05-13 17:58:14 UTC  
> Updated_at: 2023-05-13 18:01:25 UTC  
> Url: https://github.com/boostorg/random/pull/89#discussion_r1193019883  

Don't `#include <iostream>`.  Use `<istream>` and `<ostream>` unless you need `std::cin`, `std::cout`, etc.

---

📁 include/boost/random/splitmix64.hpp

```diff
  22 |+ #include <cstdlib>
  23 |+ #include <limits>
  24 |+ #include <random>
```

> Username: swatanabe  
> Created_at: 2023-05-13 17:59:09 UTC  
> Updated_at: 2023-05-13 18:01:25 UTC  
> Url: https://github.com/boostorg/random/pull/89#discussion_r1193019930  

I don't see anything using `<random>` here.

---

📁 include/boost/random/splitmix64.hpp

```diff
  13 |+  *  rather suggest to use a xoroshiro128+ (for moderately parallel
  14 |+  *  computations) or xorshift1024* (for massively parallel computations)
  15 |+  *  generator.
```

> Username: swatanabe  
> Created_at: 2023-05-13 17:59:43 UTC  
> Updated_at: 2023-05-13 18:01:25 UTC  
> Url: https://github.com/boostorg/random/pull/89#discussion_r1193020054  

All of this should be in a doxygen comment attached to the class.


---

## Comment 26

> Username: NAThompson  
> Created_at: 2023-12-14 19:14:19 UTC  
> Url: https://github.com/boostorg/random/pull/89#issuecomment-1856436185  

@mborland : LGTM. Merge it.

---
