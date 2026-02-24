# #28 Feature: Quasi-random generators [Closed]

> Username: justinasvd  
> Created at: 2017-08-31 09:12:46 UTC  
> Updated at: 2018-02-15 15:14:46 UTC  
> Closed at: 2018-02-15 15:14:46 UTC  
> Url: https://github.com/boostorg/random/pull/28  

This implements, documents, and tests quasi-random number generators. More concretely:  
  
1. Niederreiter base 2 generator;  
2. Sobol generator;  
3. Faure generator.  
  
Those generators were implemented in 2010 GSOC, but over time I've become disenchanted with the notion that dimensionality must be a compile-time option, so I rewrote the generators (and updated Sobol tables) to handle dimensionality in runtime.

---

## Comment 1

> Username: justinasvd  
> Created_at: 2017-09-06 19:42:02 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-327592046  

Personally, I think that pertinent questions regarding this pull request are these:  
  
1. Should Sobol and Niederreiter sequences start with trivial 0?  
  
I have no strong opinion. I have seen generators that start with 0, generators that skip 0. However, I would slightly prefer to skip 0, since that results in a more straightforward sequence counting. This PR, on the other hand, starts Sobol and Niederreiter sequences with 0.  
  
2. inv_qs_base in Faure generator.  
  
I added this to avoid repeated division by qs_base, but I don't know if it's worth it. Nowadays I prefer straightforward division if I can help it.   
  
3. SeedSeq in QRNGs.  
  
It is an interesting topic. I imagine that this could be used to scramble QRNGs in order to give raise to related Faure, Sobol, or Niederreiter family of sequences. Perhaps ideas from Hongmei Chi's PhD thesis "Scrambled Quasirandom Sequences and Their Applications" could be used here. The problem is that there is no single "standard" way to scramble the sequences. The best we can do is to implement some scrambling method that is known to work and then if anyone has improvements then to accept those improvements graciously.  
  
I think that about sums it up. If you have any other questions I would be happy to answer.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2018-02-12 16:38:26 UTC  
> Updated_at: 2018-02-12 19:56:22 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-364981278  

I'm trying to implement these same options in boost.math for randomized quasi-monte Carlo integration, not knowing about this work. What would it take to push this across the finish line? Does it need a review? I am willing to do this if we can get a maintainer to help out.

---

## Comment 3

> Username: justinasvd  
> Created_at: 2018-02-12 21:44:41 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-365072635  

I would be happy to cooperate. I hope that @swatanabe can share his insights as to what has to be done to push this PR to the finish line. After all, QRNGs are a big missing piece in the Random library.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2018-02-12 22:00:37 UTC  
> Updated_at: 2018-02-12 22:01:21 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-365077366  

Ok cool. I think I'm in a position to do a good code review since I did a literature review on this topic recently and have an application which uses these tools. In addition I have an implementation of the Atanassov sequence and the leaped Halton sequence which could round out the feature set nicely.

---

## Comment 5

> Username: swatanabe  
> Created_at: 2018-02-12 22:28:32 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-365084873  

AMDG  
  
  My biggest concern with QRNGs is that they don't play  
well with most distributions.  (Correct me if I'm wrong  
here, as I'm not really an expert in the domain).  In  
order for the properties of a QRNG to be useful, everything  
using them needs to take a deterministic number of samples,  
but the majority of the distributions are implemented using  
some form of rejection sampling.  At the very least,  
generate_canonical, uniform_real, and independent_bits_engine  
should work.  Most floating point distributions could  
hypothetically be implemented using inversion, but  
uniform_int_distribution is essentially impossible to  
implement subject to these constraints.  It may be enough  
to document that most distributions are not useful with  
QRNGs as long as it's made clear what can be expected to  
work.  I expect that QRNGs should typically be used for  
specific purposes by people who know what they're doing.  
  
In Christ,  
Steven Watanabe

---

## Comment 6

> Username: NAThompson  
> Created_at: 2018-02-13 00:16:49 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-365108026  

Hi Steven,  
  
I personally do not know of any other applications of quasi-random sequences other than high-dimensional numerical integration. For this purpose, you do not need the QRNG to play well with a distribution, you simply require the user to pass an integrand to the routine. It appears that my requirements are perhaps less demanding than what you would find in (say) [Lemieux's text](https://www.amazon.com/Monte-Quasi-Monte-Sampling-Springer-Statistics/dp/0387781641/).  
  
Perhaps Justinas has a more sophisticated vision and requirements than I do and can clarify his intended use.  
  
    Nick

---

## Comment 7

> Username: justinasvd  
> Created_at: 2018-02-13 10:27:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-365220751  

Roughly speaking, a distribution is just a function that 'shapes' randomness. It is certainly possible to use qrng sequences with distributions, but people should know what they are doing (exactly as they should know what they doing if they are, say, computing sin(exp(x)), yet we don't feel too distraught that this function doesn't have a nice real period as sin(x)).  
  
As for the usage of QRNGs, yes, they are mostly used in quasi-Monte Carlo (QMC) methods. There are various flavours of those methods, e.g., MCQMC, randomized QMC, etc. All of them have their use cases. For instance, simple QMC methods do not provide the statistically robust (standard) error estimates that MC methods provide, so some type of randomization is needed for practical QMC algorithms.  
  
However, whatever the QMC flavour, I am hard-pressed to remember that I used something else than Uniform(0,1) distribution (that's precisely the reason why I commented that 'it is especially useful in conjunction with class template uniform_real'). I am not saying here that some other distribution could not be useful or is incompatible, it's just that Uniform(0,1) is a very natural object to work with, you can transform it however you want.

---

## Review 8 [Commented]

> Username: NAThompson  
> Created_at: 2018-02-13 17:39:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/random/pull/28#pullrequestreview-96226740  

📁 include/boost/random/detail/qrng_base.hpp

```diff
  34 |+   typedef typename LatticeT::value_type result_type;
  35 |+ 
  36 |+   explicit qrng_base(std::size_t dimension)
```

> Username: NAThompson  
> Created_at: 2018-02-13 17:20:02 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167937694  

cppcheck tells me:  
  
```  
[include/boost/random/detail/qrng_base.hpp:36]: (warning) Member variable 'qrng_base::curr_elem' is not initialized in the constructor.  
[include/boost/random/detail/qrng_base.hpp:36]: (warning) Member variable 'qrng_base::seq_count' is not initialized in the constructor.  
```

> Username: justinasvd  
> Created_at: 2018-02-13 20:05:27 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167985205  

Will fix.

---

📁 include/boost/random/detail/qrng_base.hpp

```diff
 193 |+   LatticeT lattice;
 194 |+   std::size_t curr_elem;
 195 |+   std::size_t seq_count;
```

> Username: NAThompson  
> Created_at: 2018-02-13 17:25:13 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167939263  

In my Atanassov and Halton sequence, I wanted to be able to consume the stream from a multithreaded context, so I made the sequence count atomic. Am I correct in understanding that (1) currently the stream cannot be consumed from a multithreaded context without a race condition, and (2) that making `curr_elem` and `seq_count` atomic would allow consumption from a multithreaded context?

> Username: justinasvd  
> Created_at: 2018-02-13 20:54:45 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167997663  

I need to think about this, but at this particular moment am pretty averse to anything that makes the other users pay for what they don't use. So I wouldn't want to just slap on std::atomic here. What do you think, @swatanabe ?


📁 include/boost/random/faure.hpp

```diff
  31 |+ namespace fr {
  32 |+ 
  33 |+ // There is no particular reason why 187 first primes were chosen
```

> Username: NAThompson  
> Created_at: 2018-02-13 17:21:42 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167938277  

There is a prime table given in `boost/math/special_functions/prime.hpp`. Presumably this should be used?

> Username: NAThompson  
> Created_at: 2018-02-13 17:23:02 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167938662  

We may also drop a sieve of Eratosthenes into boost.integer. Could this get us to arbitrary dimension?

> Username: justinasvd  
> Created_at: 2018-02-13 20:52:59 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167997212  

Oh, I will gladly use some function from math header! But note that I need to know a lower bound prime for a given number `n`, as opposed to just getting an `n`-th prime. If something like this could be implemented in math section, it would be nice (besides, `prime` and `lower_bound_prime` sounds like very complementary functions).  
  
As for an arbitrary dimension, it's a bit more complicated than just plugging in a sufficiently big prime. van der Corput sequence computation would also need to be at infinite precision. It is possible, but costly.

---

📁 include/boost/random/faure.hpp

```diff
  35 |+ // the number of dimensions for Faure generator would be around
  36 |+ // the same number as the number of dimensions supported by the
  37 |+ // Sobol qrng.
```

> Username: NAThompson  
> Created_at: 2018-02-13 17:25:21 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167939304  

I'd rather see as many dimensions supported as possible, letting each qrng have it's own reasonable maximum size.

---

📁 include/boost/random/faure.hpp

```diff
  89 |+ }
  90 |+ 
  91 |+ // Perform exponentiation by squaring
```

> Username: NAThompson  
> Created_at: 2018-02-13 17:26:28 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167939629  

Use `boost::multiprecision::powm` for code reuse.

---

📁 include/boost/random/faure.hpp

```diff
 185 |+ 
 186 |+   template<typename Iterator>
 187 |+   RealType compute_recip(std::size_t seq, std::size_t n, Iterator out) const
```

> Username: NAThompson  
> Created_at: 2018-02-13 17:28:28 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167940220  

Is this a van der Corput sequence? The code looks like a van der Corput . . .


📁 include/boost/random/niederreiter_base2.hpp

```diff
  52 |+ 
  53 |+ // Maximum allowed space dimension
  54 |+ #define BOOST_RANDOM_NIEDERREITER_BASE2_MAX_DIMENSION 54
```

> Username: NAThompson  
> Created_at: 2018-02-13 17:31:06 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167940989  

Can the number of dimensions supported be increased? I personally am looking towards 30,000 dimensions.

> Username: NAThompson  
> Created_at: 2018-02-13 21:30:08 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r168007179  

This is again better than the GSL, which is only implemented up to dimension 12.

---

📁 include/boost/random/niederreiter_base2.hpp

```diff
  75 |+ };
  76 |+ 
  77 |+ // Return the base 2 logarithm for a given bitset v
```

> Username: NAThompson  
> Created_at: 2018-02-13 17:31:53 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167941212  

Can `boost/integer/integer_log2.hpp` be used here instead? Maybe not because you are using a bitset?

> Username: justinasvd  
> Created_at: 2018-02-13 20:26:14 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167990296  

Yes, precisely. dynamic bitset log here. This implementation is quite good if I may say so, but I wouldn't mind if it was moved to some other header.

---

📁 include/boost/random/niederreiter_base2.hpp

```diff
 276 |+ //!class template niederreiter_base2 implements a quasi-random number generator as described in
 277 |+ //! \blockquote
 278 |+ //!Bratley, Fox, Niederreiter, ACM Trans. Model. Comp. Sim. 2, 195 (1992).
```

> Username: NAThompson  
> Created_at: 2018-02-13 17:35:08 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167942131  

Shouldn't we deploy Neiderreiter's improved sequence from 1996 rather than the 1992 work?  
  
https://www.sciencedirect.com/science/article/pii/S1071579796900167

> Username: justinasvd  
> Created_at: 2018-02-13 20:27:34 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167990612  

I will take a look.


📁 include/boost/random/sobol.hpp

```diff
  34 |+ 
  35 |+ // Maximum allowed space dimension
  36 |+ #define BOOST_RANDOM_SOBOL_MAX_DIMENSION 1111
```

> Username: NAThompson  
> Created_at: 2018-02-13 17:36:09 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167942386  

Again, any possibility of getting this an order of magnitude higher?

> Username: justinasvd  
> Created_at: 2018-02-13 20:36:25 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167992975  

This is possible. Joe and Koe provide Sobol primitive polynomials and direction numbers up to dimension 21201.  
  
http://web.maths.unsw.edu.au/~fkuo/sobol/  
  
I trust this source, but anything else in the order of magnitude range will need to be written down, peer reviewed and published in a serious journal. ;-)

> Username: NAThompson  
> Created_at: 2018-02-13 21:27:59 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r168006594  

I think 21201 is a perfectly acceptable max dimension. It would put the capabilities of this Sobol sequence *way* beyond the capabilities of the GSL (which implements up to dimension 40).

---

📁 include/boost/random/sobol.hpp

```diff
1088 |+   }
1089 |+ 
1090 |+   //=========================Doxygen needs this!==============================
```

> Username: NAThompson  
> Created_at: 2018-02-13 17:37:38 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167942850  

If you are also writing .qbk, why vandalize your comments with doxygen syntax?


---

## Review 9 [Commented]

> Username: NAThompson  
> Created_at: 2018-02-13 17:43:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/random/pull/28#pullrequestreview-96234792  

📁 doc/concepts.qbk

```diff
 238 |+  ]
 239 |+ 
 240 |+ [note The `operator()` returns a successive element of an s-dimensional (`s` = `v.dimension()`) vector
```

> Username: NAThompson  
> Created_at: 2018-02-13 17:43:19 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167944491  

Hmm, this sounds like a lot of data movement. It's might make more sense to allow `operator()` to accept a forward iteration and have the routine fill a region of memory provided by the user.

> Username: justinasvd  
> Created_at: 2018-02-13 19:54:15 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167982187  

I already implement this interface in all QRNGs  
```  
    template<class Iter>  
    void generate(Iter first, Iter last) {/*..*/}  
```  
However, you might be surprised what you will get back. Gray-coded sequences will generate plain integers for you. You will need to do your own arithmetic to scale this to [0;1] range.

> Username: NAThompson  
> Created_at: 2018-02-13 21:08:04 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r168001258  

If I'm not mistaken, the Faure sequence populate the container with some `RealType`, whereas the Sobol and Niederreiter will populate them with integer types. This will make generic programming difficult; for instance a quasi-Monte Carlo integrator will not be able to swap out the quasi-random sequence. Can we instead do the rescaling in `generate`, and have (say) `generate_integer` for integer types?


---

## Review 10 [Commented]

> Username: NAThompson  
> Created_at: 2018-02-13 17:57:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/random/pull/28#pullrequestreview-96236656  

📁 doc/concepts.qbk

```diff
 269 | [section Seed Sequence]
 270 | 
 271 | A SeedSeq represents a sequence of values that can be used to
```

> Username: NAThompson  
> Created_at: 2018-02-13 17:48:49 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167946053  

Should the documentation give a list of known discrepancy bounds for each sequence?

> Username: NAThompson  
> Created_at: 2018-02-13 17:56:59 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167948657  

Should the docs discuss how quasi-random sequences exploit low effective dimension? See "Necessity of Low Effective Dimension" by Art Owen.

> Username: justinasvd  
> Created_at: 2018-02-13 20:03:15 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167984682  

1) Sounds ok.  
2) I think, umm... no. I think we should try and maintain some balance between this being a simple documentation of the API, and a full-blown introduction to the finer nuances of QRNGs.


📁 test/sobol_validate.cpp

```diff
 369 |+   test_sobol_discard(sobol_07_100, 128);
 370 |+   test_sobol_discard(sobol_16_100, 128);
 371 |+ }
```

> Username: NAThompson  
> Created_at: 2018-02-13 17:55:34 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167948134  

Matching published data is good, but can we compute the L2 star discrepancy and show it satisfies published bounds?


---

## Comment 11

> Username: NAThompson  
> Created_at: 2018-02-13 18:05:43 UTC  
> Updated_at: 2018-02-13 18:06:10 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-365352321  

> The best we can do is to implement some scrambling method that is known to work and then if anyone has improvements then to accept those improvements graciously.  
  
The two scrambling methods discussed in [Lemieux](https://www.amazon.com/Monte-Quasi-Monte-Sampling-Springer-Statistics/dp/0387781641/) are permutation of the coordinates and the Cranley-Patterson rotation. In my implementation, I made coordinate permutation an option in the constructor of the sequences, since permuting data is expensive. Cranley-Patterson rotation is performed by the user after the vector is delivered. It's not clear to me that this is the best way to do it.  
  
> Should Sobol and Niederreiter sequences start with trivial 0?  
  
My view is no. This is known to cause problems with numerical integration of functions singular on the boundary. Of course, the user can just read the documentation and skip the first element, but it's always better if the user doesn't have to read the docs.

---

## Comment 12

> Username: NAThompson  
> Created_at: 2018-02-13 18:36:06 UTC  
> Updated_at: 2018-02-13 18:38:26 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-365361314  

I can't get the following to compile using the develop version of multiprecision:  
  
```  
#include <iostream>  
#include <boost/random/sobol.hpp>  
  
int main()  
{  
    boost::sobol s(50);  
    return 0;  
}  
```  
  
Error:  
  
```  
make  
clang++ --std=c++1z -O3 -march=native -Wfatal-errors -I./include -I../multiprecision/include main.cpp -o main.x -pthread  
In file included from main.cpp:2:  
In file included from ./include/boost/random/sobol.hpp:12:  
In file included from ./include/boost/random/detail/gray_coded_qrng_base.hpp:18:  
../multiprecision/include/boost/multiprecision/detail/bitscan.hpp:151:28: fatal error: no type named 'uint64_t' in namespace 'boost'; did you mean simply  
      'uint64_t'?  
   union { uint128_type v; boost::uint64_t sv[2]; } val;  
                           ^~~~~~~~~~~~~~~  
                           uint64_t  
/usr/include/_types/_uint64_t.h:31:28: note: 'uint64_t' declared here  
typedef unsigned long long uint64_t;  
                           ^  
1 error generated.  
make: *** [main.x] Error 1  
```  
  
This is my "fix", I haven't verified it works: In `boost/random/detail/gray_coded_qrng_base.hpp`, change  
  
```  
//#include <boost/multiprecision/detail/bitscan.hpp> // find_lsb  
#include <boost/multiprecision/integer.hpp>  
//int r = multiprecision::detail::find_lsb(~cnt);  
int r = multiprecision::lsb(~cnt);  
```  
  
It also looks as if most everything in boost random is in namespace `boost::random`, but `sobol` is in the boost namespace.

---

## Review 13 [Commented]

> Username: NAThompson  
> Created_at: 2018-02-13 18:47:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/random/pull/28#pullrequestreview-96255585  

📁 include/boost/random/sobol.hpp

```diff
1114 |+ } // namespace random
1115 |+ 
1116 |+ typedef random::sobol<uint32_t> sobol;
```

> Username: NAThompson  
> Created_at: 2018-02-13 18:46:52 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r167962772  

If generating a point in the Sobol sequence takes ~50ns (haven't done performance testing, just seems to be a reasonable assumption), then shouldn't a 32 bit number overflow in roughly 3-4 minutes, on a single thread? This default seems likes it's too small; a high dimensional numerical integration can last for days. The default typedef should probably be 64 bit, no?

> Username: justinasvd  
> Created_at: 2018-02-14 09:51:20 UTC  
> Updated_at: 2018-02-15 15:13:56 UTC  
> Url: https://github.com/boostorg/random/pull/28#discussion_r168123070  

I think you are right. If I parametrize Sobol lattice bits as uint32_t, then it naturally gives seq_count just up to 2^32-1 (trivial zeros are skipped).  
  
Perhaps the default should indeed be uint64_t nowadays. I should also probably store seq_count as that IntType, and update seed and discard member fn param types appropriately.


---

## Comment 14

> Username: swatanabe  
> Created_at: 2018-02-13 18:48:21 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-365364930  

AMDG  
  
On 02/13/2018 11:36 AM, Nick wrote:  
> I can't get the following to compile using the develop version of multiprecision:  
>   
> ../multiprecision/include/boost/multiprecision/detail/bitscan.hpp:151:28: fatal error: no type named 'uint64_t' in namespace 'boost';  
>   
>   
  
This is a bug in multiprecision.  boost::uint64_t is  
declared in boost/cstdint.hpp.  
  
In Christ,  
Steven Watanabe

---

## Comment 15

> Username: NAThompson  
> Created_at: 2018-02-13 18:58:11 UTC  
> Updated_at: 2018-02-13 19:12:49 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-365368020  

I was definitely surprised by the API. I had expected:  
  
```  
#include <iostream>  
#include <boost/random/sobol.hpp>  
  
int main()  
{  
    boost::sobol s(50);  
    int i = 0;  
    std::cout << s() << std::endl;  
    return 0;  
}  
```  
  
to print 50 numbers. But instead each call prints a single number. I think others will be surprised as well. Let me show you the usage of my leaped Halton sequence:  
  
```  
// Construct Halton sequence class:  
boost::math::halton_sequence<double> hs(50);  
// This is where data will be placed:  
std::vector<double> x(50);  
hs(x.begin(), x.end());  
// Now x is full of the data from the Halton sequence.  
```  
  
You can look at my file, which is much less sophisticated than your generators [here](https://github.com/boostorg/math/blob/9d4752bc5671fad664f311f23fccbf49ec509b45/include/boost/math/tools/halton_sequence.hpp).  
  
Hopefully I'm not being close minded in preferring my API, but I think this is preferable as the data movement is much less, and the user only has to do a single malloc and afterwards can reuse that memory space. In addition, there is a natural boundary between points, whereas there is no boundary in the current Sobol API.  
  
Maybe your streams have some wizardry under the hood I'm not seeing?

---

## Comment 16

> Username: justinasvd  
> Created_at: 2018-02-13 20:16:11 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-365390565  

You can use the API the way you would like, but you have to follow Boost.Random conventions.  
  
For instance, the thing you want could be written like this:  
```C++  
#include <boost/random/sobol.hpp>  
#include <boost/random/uniform_01.hpp>  
#include <boost/random/variate_generator.hpp>  
  
int main()  
{  
    static const std::size_t dimension = 4;  
  
    // Create a generator  
    typedef boost::variate_generator<boost::sobol&, boost::uniform_01<double> > quasi_random_gen_t;  
  
    // Initialize the engine to draw randomness out of thin air  
    boost::sobol engine(dimension);  
  
    // Glue the engine and the distribution together  
    quasi_random_gen_t gen(engine, boost::uniform_01<double>());  
  
    std::vector<double> sample(dimension);  
  
    // At this point you can use std::generate, generate member f-n, etc.  
    std::generate(sample.begin(), sample.end(), gen);  
    engine.generate(sample.begin(), sample.end());  
}  
```  
Note the subtle difference between std::generate via `gen`, and the direct call to `engine`.generate.

---

## Comment 17

> Username: swatanabe  
> Created_at: 2018-02-13 21:14:55 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-365406461  

AMDG  
  
On 02/13/2018 02:08 PM, Nick wrote:  
>  
> If I'm not mistaken, the Faure sequence populate the container with some `RealType`, whereas the Sobol and Niederreiter will populate them with integer types. This will make generic programming difficult; for instance a quasi-Monte Carlo integrator will not be able to swap out the quasi-random sequence. Can we instead do the rescaling in `generate`, and have (say) `generate_integer` for integer types?  
>   
  
This is exactly what uniform_real_distribution is for.  
  
In Christ,  
Steven Watanabe

---

## Comment 18

> Username: swatanabe  
> Created_at: 2018-02-13 21:20:26 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-365407963  

AMDG  
  
On 02/13/2018 01:54 PM, Justinas V. Daugmaudis wrote:  
> <snip>  
> I need to think about this, but at this particular moment am pretty averse to anything that makes the other users pay for what they don't use. So I wouldn't want to just slap on std::atomic here. What do you think, @swatanabe ?  
>   
  
  Definitely no.  Nothing else in Boost.Random allows  
parallel reads from the same engine.  Also, for  
QRNGs I don't see how making it atomic would be useful  
as interleaved reads will completely mess up the properties  
that you're using a QRNG for in the first place.  
  
In Christ,  
Steven Watanabe

---

## Comment 19

> Username: NAThompson  
> Created_at: 2018-02-13 21:23:41 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-365408870  

Ok, I've ran all the generators through the undefined behavior and address sanitizer and they come up clean. In addition I ran a test using multiprecision, and no compile or runtime errors were found. So it looks quite nice to me.  
  
Maybe post it on codereview.stackexchange and see what they say as well?

---

## Comment 20

> Username: justinasvd  
> Created_at: 2018-02-13 21:31:55 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-365411132  

I imagine the scenario where a user might want to get full s-dimensional vectors from an engine and process those vectors in parallel. For instance, this could be used in some kind of optimization algorithm where QRNG would provide 'grid points' for the computation. I do something like this when I perform global optimization for biochemical kinetics problems. But interleaved reads via operator() – absolutely no, doesn't make much sense.  
  
The bottom line: if you want to use the same engine for producing grid points for the s-dimensional space, you will have to perform a lock on generate. Alas, I don't see any other solution. In my own case it looks like this:  
```C++  
        #pragma omp critical (QuasiRandomSampling)  
        {  
          std::generate(sample.begin(), sample.end(), gen);  
        }  
```

---

## Comment 21

> Username: NAThompson  
> Created_at: 2018-02-13 21:49:11 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-365416187  

Interleaved reads do indeed make sense in the context of numerical integration. You may wish to evaluate a function at each point generated by the quasi-random sequence and then you reduce each individual thread estimate into the final value. However, using randomized quasi-Monte Carlo integration (which has better asymptotic convergence) you want each thread to have it's own QRNG, and perhaps apply coordinate permutation and/or a Cranley-Patterson rotation. These should be done independently of the other threads. So, at least for my use case, it is fine to not make the reads atomic.  
  
But this raises another question: Do you think that these randomization strategies should be supported within the classes, or should they be done as a postprocessing step? And if they are done as a postprocessing step, does boost.random provide the most common ones?

---

## Comment 22

> Username: justinasvd  
> Created_at: 2018-02-13 22:03:21 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-365420303  

I think we are talking about different things when we say 'interleaved reads'. You are probably talking about reading a full s-dimensional vector kinda atomically and in this context interleaved reads would be fine. You then simply parallely process different points in a s-dimensional hypercube. This is legit and this could be achieved via full lock as shown above. There is no other way to mutate engine state sequentially.  
  
What we are talking about when we say 'interleaved reads', we are talking about interleaved memory access in an engine (a much lower level stuff). This can't work in a meaningful way.

---

## Comment 23

> Username: NAThompson  
> Created_at: 2018-02-14 17:59:15 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-365691963  

A few more things:  
  
- Could you put the code you just wrote in the comment in (say) `example/qrng.cpp` and perhaps link it into the docs?  
  
- What is supposed to happen when I request a QRNG which exceeds (say) SOBOL_MAX_DIMENSION? I tried this and I got no error, nor did I see any discussion of the expected behavior in the docs.  
  
- The call to `std::generate` is an order of magnitude slower than the call to `engine.generate` in your example. Is this expected?

---

## Comment 24

> Username: swatanabe  
> Created_at: 2018-02-14 18:20:30 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-365698015  

AMDG  
  
On 02/14/2018 10:59 AM, Nick wrote:  
>   
> - The call to `std::generate` is an order of magnitude slower than the call to `engine.generate` in your example. Is this expected?  
>   
  
  QRNGs should not implement generate.  The purpose of  
generate is to allow a random engine to be a SeedSeq,  
but seeding an engine with a QRNG is not useful.  uniform_01  
uses floating point arithmetic (including a divide), so  
it's not surprising that it's slower than engine.generate,  
which reduces to a straight copy of the internal state.  
  
In Christ,  
Steven Watanabe

---

## Comment 25

> Username: NAThompson  
> Created_at: 2018-02-14 18:29:09 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-365700554  

Just ran a performance test over every input I could think of, with and without sanitizers. With sanitizers, it comes up clean. Without, the generators blaze:  
  
```  
#include <benchmark/benchmark.h>  
#include <ostream>  
#include <boost/random/faure.hpp>  
#include <boost/random/sobol.hpp>  
#include <boost/random/niederreiter_base2.hpp>  
#include <boost/random/uniform_01.hpp>  
#include <boost/random/variate_generator.hpp>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
  
using boost::multiprecision::cpp_bin_float_quad;  
  
template<class Real>  
void BM_Sobol(benchmark::State& state)  
{  
    static const std::size_t dimension = state.range(0);  
    // Create a generator  
    typedef boost::variate_generator<boost::sobol&, boost::uniform_01<Real> > quasi_random_gen_t;  
    // Initialize the engine to draw randomness out of thin air  
    boost::sobol engine(dimension);  
    // Glue the engine and the distribution together  
    quasi_random_gen_t gen(engine, boost::uniform_01<Real>());  
    std::vector<Real> sample(dimension);  
    for (auto _ : state)  
    {  
        engine.generate(sample.begin(), sample.end());  
        //std::generate(sample.begin(), sample.end(), gen);  
        benchmark::DoNotOptimize(sample[0]);  
    }  
    state.SetComplexityN(state.range(0));  
    std::ostream cnull(0);  
    cnull << sample[dimension-1];  
}  
  
BENCHMARK_TEMPLATE(BM_Sobol, float)->RangeMultiplier(2)->Range(1, 1111)->Complexity();  
BENCHMARK_TEMPLATE(BM_Sobol, double)->RangeMultiplier(2)->Range(1, 1111)->Complexity();  
BENCHMARK_TEMPLATE(BM_Sobol, long double)->RangeMultiplier(2)->Range(1, 1111)->Complexity();  
BENCHMARK_TEMPLATE(BM_Sobol, cpp_bin_float_quad)->RangeMultiplier(2)->Range(1, 1111)->Complexity();  
  
  
template<class Real>  
void BM_Niederreiter(benchmark::State& state)  
{  
    static const std::size_t dimension = state.range(0);  
    // Create a generator  
    typedef boost::variate_generator<boost::niederreiter_base2&, boost::uniform_01<Real> > quasi_random_gen_t;  
    // Initialize the engine to draw randomness out of thin air  
    boost::niederreiter_base2 engine(dimension);  
    // Glue the engine and the distribution together  
    quasi_random_gen_t gen(engine, boost::uniform_01<Real>());  
    std::vector<Real> sample(dimension);  
    for (auto _ : state)  
    {  
        engine.generate(sample.begin(), sample.end());  
        benchmark::DoNotOptimize(sample[0]);  
    }  
    state.SetComplexityN(state.range(0));  
    std::ostream cnull(0);  
    cnull << sample[dimension-1];  
}  
  
BENCHMARK_TEMPLATE(BM_Niederreiter, float)->RangeMultiplier(2)->Range(1, 54)->Complexity();  
BENCHMARK_TEMPLATE(BM_Niederreiter, double)->RangeMultiplier(2)->Range(1, 54)->Complexity();  
BENCHMARK_TEMPLATE(BM_Niederreiter, long double)->RangeMultiplier(2)->Range(1, 54)->Complexity();  
BENCHMARK_TEMPLATE(BM_Niederreiter, cpp_bin_float_quad)->RangeMultiplier(2)->Range(1, 54)->Complexity();  
  
template<class Real>  
void BM_Faure(benchmark::State& state)  
{  
    static const std::size_t dimension = state.range(0);  
    // Create a generator  
    typedef boost::variate_generator<boost::faure&, boost::uniform_01<Real> > quasi_random_gen_t;  
    // Initialize the engine to draw randomness out of thin air  
    boost::faure engine(dimension);  
    // Glue the engine and the distribution together  
    quasi_random_gen_t gen(engine, boost::uniform_01<Real>());  
    std::vector<Real> sample(dimension);  
    for (auto _ : state)  
    {  
        engine.generate(sample.begin(), sample.end());  
        benchmark::DoNotOptimize(sample[0]);  
    }  
    state.SetComplexityN(state.range(0));  
    std::ostream cnull(0);  
    cnull << sample[dimension-1];  
}  
  
BENCHMARK_TEMPLATE(BM_Faure, float)->RangeMultiplier(2)->Range(1, 187)->Complexity();  
BENCHMARK_TEMPLATE(BM_Faure, double)->RangeMultiplier(2)->Range(1, 187)->Complexity();  
BENCHMARK_TEMPLATE(BM_Faure, long double)->RangeMultiplier(2)->Range(1, 187)->Complexity();  
BENCHMARK_TEMPLATE(BM_Faure, cpp_bin_float_quad)->RangeMultiplier(2)->Range(1, 187)->Complexity();  
  
BENCHMARK_MAIN();  
```  
  
Results on my machine:  
  
```  
clang++ --std=c++1z -O3 -march=native -Wfatal-errors -I./include -I../multiprecision/include main.cpp -o main.x -pthread -lbenchmark  
2018-02-14 12:22:52  
Run on (4 X 2700 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x2)  
  L1 Instruction 32K (x2)  
  L2 Unified 262K (x2)  
  L3 Unified 3145K (x1)  
------------------------------------------------------------------------------  
Benchmark                                       Time           CPU Iterations  
------------------------------------------------------------------------------  
BM_Sobol<float>/1                               9 ns          8 ns   73408350  
BM_Sobol<float>/2                               9 ns          9 ns   86164451  
BM_Sobol<float>/4                               8 ns          8 ns   80164911  
BM_Sobol<float>/8                               8 ns          8 ns   84340398  
BM_Sobol<float>/16                              9 ns          9 ns   82468397  
BM_Sobol<float>/32                              8 ns          8 ns   83769132  
BM_Sobol<float>/64                              8 ns          8 ns   84947333  
BM_Sobol<float>/128                             9 ns          9 ns   82347128  
BM_Sobol<float>/256                             9 ns          9 ns   79820290  
BM_Sobol<float>/512                             9 ns          9 ns   84290634  
BM_Sobol<float>/1024                            9 ns          8 ns   75714147  
BM_Sobol<float>/1111                            8 ns          8 ns   83925810  
BM_Sobol<float>_BigO                         8.67 (1)       8.55 (1)  
BM_Sobol<float>_RMS                             4 %          3 %  
BM_Sobol<double>/1                              9 ns          9 ns   82954115  
BM_Sobol<double>/2                             10 ns          9 ns   80483823  
BM_Sobol<double>/4                              9 ns          9 ns   78106694  
BM_Sobol<double>/8                              9 ns          9 ns   79294056  
BM_Sobol<double>/16                             9 ns          9 ns   80215436  
BM_Sobol<double>/32                             9 ns          9 ns   80276150  
BM_Sobol<double>/64                             9 ns          9 ns   74150186  
BM_Sobol<double>/128                            9 ns          9 ns   79385781  
BM_Sobol<double>/256                            9 ns          9 ns   78222778  
BM_Sobol<double>/512                            9 ns          9 ns   68710982  
BM_Sobol<double>/1024                           9 ns          9 ns   70818664  
BM_Sobol<double>/1111                           9 ns          9 ns   83342263  
BM_Sobol<double>_BigO                        8.99 (1)       8.77 (1)  
BM_Sobol<double>_RMS                            4 %          2 %  
BM_Sobol<long double>/1                        11 ns         11 ns   58681018  
BM_Sobol<long double>/2                        12 ns         11 ns   58701686  
BM_Sobol<long double>/4                        12 ns         12 ns   58002718  
BM_Sobol<long double>/8                        17 ns         14 ns   61996280  
BM_Sobol<long double>/16                       12 ns         11 ns   60961272  
BM_Sobol<long double>/32                       12 ns         11 ns   62080403  
BM_Sobol<long double>/64                       11 ns         11 ns   57786290  
BM_Sobol<long double>/128                      11 ns         11 ns   60893393  
BM_Sobol<long double>/256                      11 ns         11 ns   61409434  
BM_Sobol<long double>/512                      12 ns         11 ns   45885091  
BM_Sobol<long double>/1024                     12 ns         12 ns   55659807  
BM_Sobol<long double>/1111                     12 ns         12 ns   62202870  
BM_Sobol<long double>_BigO                  12.02 (1)      11.64 (1)  
BM_Sobol<long double>_RMS                      13 %          6 %  
BM_Sobol<cpp_bin_float_quad>/1                 12 ns         11 ns   59808612  
BM_Sobol<cpp_bin_float_quad>/2                 12 ns         11 ns   67643307  
BM_Sobol<cpp_bin_float_quad>/4                 11 ns         10 ns   61240737  
BM_Sobol<cpp_bin_float_quad>/8                 12 ns         11 ns   65265023  
BM_Sobol<cpp_bin_float_quad>/16                11 ns         10 ns   67434781  
BM_Sobol<cpp_bin_float_quad>/32                11 ns         11 ns   65736958  
BM_Sobol<cpp_bin_float_quad>/64                11 ns         11 ns   64497107  
BM_Sobol<cpp_bin_float_quad>/128               11 ns         11 ns   61562816  
BM_Sobol<cpp_bin_float_quad>/256               11 ns         11 ns   57533143  
BM_Sobol<cpp_bin_float_quad>/512               10 ns         10 ns   66699063  
BM_Sobol<cpp_bin_float_quad>/1024              11 ns         10 ns   64954346  
BM_Sobol<cpp_bin_float_quad>/1111              11 ns         10 ns   64142506  
BM_Sobol<cpp_bin_float_quad>_BigO           11.08 (1)      10.66 (1)  
BM_Sobol<cpp_bin_float_quad>_RMS                5 %          3 %  
BM_Niederreiter<float>/1                       10 ns          9 ns   83076193  
BM_Niederreiter<float>/2                        9 ns          9 ns   79733917  
BM_Niederreiter<float>/4                        9 ns          9 ns   81638365  
BM_Niederreiter<float>/8                        9 ns          9 ns   76788065  
BM_Niederreiter<float>/16                       9 ns          9 ns   80789428  
BM_Niederreiter<float>/32                       9 ns          9 ns   75182317  
BM_Niederreiter<float>/54                       9 ns          9 ns   76806601  
BM_Niederreiter<float>_BigO                  8.99 (1)       8.86 (1)  
BM_Niederreiter<float>_RMS                      4 %          3 %  
BM_Niederreiter<double>/1                       9 ns          9 ns   78133720  
BM_Niederreiter<double>/2                       9 ns          8 ns   81273439  
BM_Niederreiter<double>/4                       9 ns          9 ns   80682342  
BM_Niederreiter<double>/8                       9 ns          9 ns   79509314  
BM_Niederreiter<double>/16                      9 ns          9 ns   82313237  
BM_Niederreiter<double>/32                      9 ns          9 ns   84216605  
BM_Niederreiter<double>/54                      9 ns          9 ns   81195193  
BM_Niederreiter<double>_BigO                 8.69 (1)       8.59 (1)  
BM_Niederreiter<double>_RMS                     1 %          1 %  
BM_Niederreiter<long double>/1                 12 ns         12 ns   64011120  
BM_Niederreiter<long double>/2                 12 ns         11 ns   62615727  
BM_Niederreiter<long double>/4                 12 ns         12 ns   59242208  
BM_Niederreiter<long double>/8                 11 ns         11 ns   62929833  
BM_Niederreiter<long double>/16                11 ns         11 ns   61409434  
BM_Niederreiter<long double>/32                11 ns         11 ns   60407839  
BM_Niederreiter<long double>/54                11 ns         11 ns   61253063  
BM_Niederreiter<long double>_BigO           11.67 (1)      11.42 (1)  
BM_Niederreiter<long double>_RMS                3 %          1 %  
BM_Niederreiter<cpp_bin_float_quad>/1          11 ns         11 ns   66870462  
BM_Niederreiter<cpp_bin_float_quad>/2          11 ns         11 ns   65542457  
BM_Niederreiter<cpp_bin_float_quad>/4          11 ns         11 ns   68601893  
BM_Niederreiter<cpp_bin_float_quad>/8          12 ns         12 ns   66257762  
BM_Niederreiter<cpp_bin_float_quad>/16         11 ns         11 ns   66526012  
BM_Niederreiter<cpp_bin_float_quad>/32         11 ns         11 ns   68570309  
BM_Niederreiter<cpp_bin_float_quad>/54         11 ns         11 ns   57759588  
BM_Niederreiter<cpp_bin_float_quad>_BigO      10.88 (1)      10.80 (1)  
BM_Niederreiter<cpp_bin_float_quad>_RMS          4 %          3 %  
BM_Faure<float>/1                             489 ns        472 ns    1512098  
BM_Faure<float>/2                             479 ns        471 ns    1512477  
BM_Faure<float>/4                             485 ns        473 ns    1525207  
BM_Faure<float>/8                             485 ns        482 ns    1494338  
BM_Faure<float>/16                            492 ns        482 ns    1525523  
BM_Faure<float>/32                            502 ns        485 ns    1517697  
BM_Faure<float>/64                            475 ns        472 ns    1519209  
BM_Faure<float>/128                           492 ns        482 ns    1512608  
BM_Faure<float>/187                           474 ns        471 ns    1499401  
BM_Faure<float>_BigO                       485.83 (1)     476.69 (1)  
BM_Faure<float>_RMS                             2 %          1 %  
BM_Faure<double>/1                            482 ns        476 ns    1501585  
BM_Faure<double>/2                            504 ns        485 ns    1515545  
BM_Faure<double>/4                            477 ns        475 ns    1503372  
BM_Faure<double>/8                            460 ns        459 ns    1534741  
BM_Faure<double>/16                           477 ns        474 ns    1503036  
BM_Faure<double>/32                           478 ns        474 ns    1526132  
BM_Faure<double>/64                           486 ns        483 ns    1483554  
BM_Faure<double>/128                          472 ns        470 ns    1546585  
BM_Faure<double>/187                          483 ns        480 ns    1506961  
BM_Faure<double>_BigO                      479.91 (1)     475.04 (1)  
BM_Faure<double>_RMS                            2 %          2 %  
BM_Faure<long double>/1                       474 ns        471 ns    1487510  
BM_Faure<long double>/2                       473 ns        469 ns    1484246  
BM_Faure<long double>/4                       471 ns        470 ns    1525866  
BM_Faure<long double>/8                       477 ns        475 ns    1505895  
BM_Faure<long double>/16                      532 ns        488 ns    1529373  
BM_Faure<long double>/32                      496 ns        478 ns    1516619  
BM_Faure<long double>/64                      476 ns        473 ns    1526481  
BM_Faure<long double>/128                     557 ns        500 ns    1440979  
BM_Faure<long double>/187                     488 ns        482 ns    1481481  
BM_Faure<long double>_BigO                 493.79 (1)     478.42 (1)  
BM_Faure<long double>_RMS                       6 %          2 %  
BM_Faure<cpp_bin_float_quad>/1                531 ns        526 ns    1000000  
BM_Faure<cpp_bin_float_quad>/2                537 ns        533 ns    1201387  
BM_Faure<cpp_bin_float_quad>/4                557 ns        547 ns    1000000  
BM_Faure<cpp_bin_float_quad>/8                536 ns        533 ns    1000000  
BM_Faure<cpp_bin_float_quad>/16               545 ns        527 ns    1000000  
BM_Faure<cpp_bin_float_quad>/32               547 ns        528 ns    1000000  
BM_Faure<cpp_bin_float_quad>/64               529 ns        525 ns    1000000  
BM_Faure<cpp_bin_float_quad>/128              590 ns        549 ns    1000000  
BM_Faure<cpp_bin_float_quad>/187              534 ns        518 ns    1000000  
BM_Faure<cpp_bin_float_quad>_BigO          545.13 (1)     531.72 (1)  
BM_Faure<cpp_bin_float_quad>_RMS                3 %          2 %  
```  
  
I'd expected O(dimension) complexity, so was surprised that the generator produced O(1) complexity. Perhaps I need to work a bit harder to fool the optimizer, though generally google benchmark's 'DoNotOptimize' works as expected.  
  
Modulo that caveat, these look very well implemented and fast.

---

## Comment 26

> Username: justinasvd  
> Created_at: 2018-02-14 20:25:05 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-365734095  

Yay, thanks. I am impressed by the effort you put into this review.  
  
I still want to verify some things. Perhaps to put the code through the MS compiler, to kill any remaining warnings, to see that the documentation looks okay*, and then I will update the PR. What do you think, @swatanabe ?  
  
---  
* Perhaps it would be prudent to add that qrngs and, say, gamma distribution combo won't give good results. For the same reason qrngs won't work with beta distribution, but IIRC normal, lognormal, exponential, pareto, etc. should work just fine.

---

## Comment 27

> Username: swatanabe  
> Created_at: 2018-02-14 20:47:22 UTC  
> Url: https://github.com/boostorg/random/pull/28#issuecomment-365740037  

AMDG  
  
On 02/14/2018 01:25 PM, Justinas V. Daugmaudis wrote:  
> Yay, thanks. I am impressed by the effort you put into this review.  
>   
> I still want to verify some things. Perhaps to put the code through the MS compiler,  
  
I'll check VC9+.  
  
> to kill any remaining warnings, to see that the documentation looks okay*, and then I will update the PR. What do you think, @swatanabe ?  
>   
  
- Please don't add names to namespace boost.  I've been  
  trying to get rid of these.  
- Consequently, you shouldn't have both sobol and sobol<T>.  
  The standard library uses the convention of calling the  
  template xxx_engine, but it may be enough just to have  
  a default template parameter and no typedef.  
  
> ---  
> * Perhaps it would be prudent to add that qrngs and, say, gamma distribution combo won't give good results. For the same reason qrngs won't work with beta distribution, but IIRC normal, lognormal, exponential, pareto, etc. should work just fine.  
>   
  
  They don't work anymore.  Also, I reserve the right  
to change the implementation of any distribution.  It  
would probably be best to leave it at: uniform_real_distribution  
works (I think it actually doesn't right now, which needs  
to be fixed).  Use any other distribution at your own risk.  
  
In Christ,  
Steven Watanabe

---
