# #37 Feature: Quasi-random generators, round III [Merged]

> Username: justinasvd  
> Created at: 2018-02-18 09:12:54 UTC  
> Updated at: 2018-05-13 03:41:48 UTC  
> Merged at: 2018-05-13 02:04:43 UTC  
> Closed at: 2018-05-13 02:04:43 UTC  
> Url: https://github.com/boostorg/random/pull/37  

This is a rebranched (from master to develop) PR of QRNGs.

---

## Comment 1

> Username: justinasvd  
> Created_at: 2018-02-20 21:14:18 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-367121547  

I have checked and rechecked, and I am fairly confident that the code should not eat anyone's dog.  
  
By the way, @swatanabe , have you had a chance to see what the latest MS compiler says?

---

## Comment 2

> Username: swatanabe  
> Created_at: 2018-02-20 21:31:16 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-367126812  

It was fine with msvc9 and up last time I checked.  I haven't run the latest commit.

---

## Comment 3

> Username: justinasvd  
> Created_at: 2018-02-20 21:46:18 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-367131121  

Should be fine, too, then. The latest commit simply allows the compiler to reuse div result, since this opcode yields both the quotient and the remainder of the division (in eax:edx registers). Apparently, x / y followed by x % y is a trivial optimization that gcc performs even at -O1.

---

## Review 4 [Commented]

> Username: swatanabe  
> Created_at: 2018-02-20 23:46:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/random/pull/37#pullrequestreview-98011535  

📁 include/boost/random/niederreiter_base2.hpp

```diff
  47 |+   size_type low = v.find_next(0);
  48 |+ 
  49 |+   // Binary lookup for the most significant set bit
```

> Username: swatanabe  
> Created_at: 2018-02-20 22:17:55 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r169478775  

A binary search doesn't make much sense, given that find_next itself uses a linear search.  It would make more sense just to implement this from scratch scanning from the end.  Actually, there's no need to recompute this at all, as the degree of the polynomial increases by exactly degree, for every iteration of the loop in resize.

> Username: justinasvd  
> Created_at: 2018-02-21 01:16:54 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r169511468  

Alas, this function is necessary, since pb_degree does not increase monotonically. It is quite possible for kj (the previous pb_degree value) to be bigger than the next computed pb_degree. Try and printout the cases, when kj > pb_degree.  
  
As for the reverse scan, you are right. I somehow didn't think of it. Done.  
  
Nevertheless, I will note that this binary search technique is not totally without merit. It effectively cuts off all the lower bits if any upper bits are already set.

> Username: justinasvd  
> Created_at: 2018-02-21 09:59:42 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r169584689  

I have tested the performance of the revscan approach. I have to say that your reverse scan idea works slightly faster for boost::uint_least64_t sized bitsets than bin search. Both revscan and bin search beat silly   
  
```C++  
  size_type low = 0;  
  size_type p = 0;  
  
  // Linear lookup for the most significant set bit  
  while (p != DynamicBitset::npos)  
  {  
    low = p;  
    p = v.find_next(low);  
  }  
  
  return low;  
```  
  
hands down.  
  
Timings on my machine for 10000000 random bitsets are like this:  
bin_search: 2.35857s  
rev_scan: 2.33807s  
lin_search: 4.01772s

---

📁 include/boost/random/niederreiter_base2.hpp

```diff
 200 |+           // Niederreiter's C(I,J,R).
 201 |+           for (unsigned r = 0; r != bit_count; ++r) {
 202 |+             ci[r][j] = v[r + u];
```

> Username: swatanabe  
> Created_at: 2018-02-20 22:35:20 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r169483091  

You're iterating over ci in column major order here.  Interchanging the loop is theoretically more efficient.  Also, I feel like it should be possible run this whole loop in a single step with an appropriate view.

> Username: justinasvd  
> Created_at: 2018-02-21 01:27:23 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r169513013  

I get your idea that you want to put, say ci's column to the function that computes `v` and, perhaps, avoid `v` altogether, but I don't think that idea will fly. At least not without some major algorithmic complications.  
  
As for the iteration order, I iterate in column major here so that I could iterate in row major a few lines below. ;-) If I change the order in one place, I will need to change the order in another place.

---

📁 include/boost/random/niederreiter_base2.hpp

```diff
 150 |+     boost::multi_array<value_type, 2> ci(boost::extents[bit_count][bit_count]);
 151 |+ 
 152 |+     std::vector<value_type> v;
```

> Username: swatanabe  
> Created_at: 2018-02-20 22:56:45 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r169487902  

As far as I can make out, both ci and v can only store 0 or 1 as elements.  You don't need to use value_type.

> Username: justinasvd  
> Created_at: 2018-02-21 01:28:32 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r169513334  

Well noted.

---

📁 include/boost/random/niederreiter_base2.hpp

```diff
  42 |+ 
  43 |+   if (v.none())
  44 |+     throw std::invalid_argument("bitset_log2");
```

> Username: swatanabe  
> Created_at: 2018-02-20 22:58:50 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r169488367  

You're inconsistent about how you throw exceptions.  (boost::throw_exception)

> Username: justinasvd  
> Created_at: 2018-02-21 01:28:43 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r169513371  

Done.

---

📁 include/boost/random/niederreiter_base2.hpp

```diff
 283 |+   std::size_t dimension() const { return base_t::dimension(); }
 284 |+ 
 285 |+   //!Requirements: *this is mutable.
```

> Username: swatanabe  
> Created_at: 2018-02-20 23:00:47 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r169488766  

That's a pointless thing to say, since the non-const signature says as much.

---

📁 include/boost/random/niederreiter_base2.hpp

```diff
 339 |+   //!
 340 |+   //!Throws: overflow_error.
 341 |+   void discard(IntType z)
```

> Username: swatanabe  
> Created_at: 2018-02-20 23:14:46 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r169491607  

IntType is wrong.  It should always be unsigned long long (boost::uinmax_t).  Consider the case where IntType = uint8_t, with 2 dimensions.  The generator can produce 256 vectors, which is 512 elements, which is more than IntType can represent.

---

📁 include/boost/random/niederreiter_base2.hpp

```diff
 133 |+   typedef IntType value_type;
 134 |+ 
 135 |+   BOOST_STATIC_CONSTANT(unsigned, bit_count = std::numeric_limits<value_type>::digits);
```

> Username: swatanabe  
> Created_at: 2018-02-20 23:22:58 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r169493220  

This should be a template parameter, rather than being deduced from the size of the argument.  The goal is to make it so that the output of the generator is platform independent.

---

📁 include/boost/random/niederreiter_base2.hpp

```diff
 101 |+   // Now choose values of V in accordance with
 102 |+   // the conditions in section 3.3.
 103 |+   std::fill(v.begin(), v.begin() + kj, T());
```

> Username: swatanabe  
> Created_at: 2018-02-20 23:26:34 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r169493878  

There's also std::fill_n.

---

📁 include/boost/random/niederreiter_base2.hpp

```diff
 109 |+   // Whatever.
 110 |+   for (size_type r = kj; r < pb_degree; ++r)
 111 |+     v[r] = arbitrary_element;
```

> Username: swatanabe  
> Created_at: 2018-02-20 23:29:04 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r169494357  

std::fill.

---

📁 include/boost/random/niederreiter_base2.hpp

```diff
 119 |+     for (int k = -pb_degree; k < 0; ++k, pb_c >>= 1)
 120 |+     {
 121 |+       if( pb_c.test(0) )
```

> Username: swatanabe  
> Created_at: 2018-02-20 23:34:05 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r169495219  

This is equivalent to pb.test(k + pb_degree).

> Username: justinasvd  
> Created_at: 2018-02-21 00:18:44 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r169502493  

Yay, an excellent idea. How did I miss this.

---

📁 include/boost/random/niederreiter_base2.hpp

```diff
 122 |+         term ^= v[r + k];
 123 |+     }
 124 |+     v[r] = term;
```

> Username: swatanabe  
> Created_at: 2018-02-20 23:43:30 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r169496726  

The body of this loop is equivalent to:  
```  
dynamic_bitset<> tmp = (v >> (r - pb_degree));  
tmp.resize(pb.size());  
v[r] = (tmp & pb).count() % 2;  
```  
if you change v to be another dynamic_bitset.

> Username: justinasvd  
> Created_at: 2018-02-21 00:18:54 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r169502521  

Changed v to dynamic_bitset, but kept the loop to check pb.test(k + pb_degree). Less temporaries.


---

## Comment 5

> Username: justinasvd  
> Created_at: 2018-02-21 20:39:28 UTC  
> Updated_at: 2018-02-22 07:08:41 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-367464281  

@swatanabe , I think that niederreiter code is pretty compact now. I removed the intermediate ci bits altogether.

---

## Comment 6

> Username: justinasvd  
> Created_at: 2018-02-23 06:38:49 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-367923795  

All the raised issues were addressed, please review again.

---

## Comment 7

> Username: NAThompson  
> Created_at: 2018-02-26 16:23:44 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-368559153  

I wonder if we can trade @swatanabe some bug fix triage for this getting merged; maintainer is a thankless job: They get to fix a bunch of code written by other people. But if we could credibly commit to fixing tickets, then perhaps it'll go a long way towards getting this into 1.67.

---

## Comment 8

> Username: justinasvd  
> Created_at: 2018-02-26 16:37:33 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-368563931  

I see no problems to fix QRNGs should we get any tickets. But I have to say that Steven's review was invaluable. The code got leaner, more concise and faster.  
  
So I don't mind waiting until he gets some time for code review. Bug fixing promises aside, I also want to meet his standards. ;-)

---

## Comment 9

> Username: swatanabe  
> Created_at: 2018-02-26 16:49:35 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-368568111  

AMDG  
  
On 02/26/2018 09:23 AM, Nick wrote:  
> I wonder if we can trade @swatanabe some bug fix triage for this getting merged; maintainer is a thankless job: They get to fix a bunch of code written by other people. But if we could credibly commit to fixing tickets, then perhaps it'll go a long way towards getting this into 1.67.  
>   
  
I think niederreiter_base2 is good now.  I still need to  
find time to check the other engines and the common  
code in qrng_base.  
  
- I think I saw some potential integer overflow in discard.  
  You should probably test the limits of discard with  
  xxx_engine<uint8_t, 7>, with three possible values  
  - The maximum value that doesn't throw  
  - The smallest value that does throw  
  - 2^k, where k is large enough to cause wrapping,  
    when truncating to uint8_t.  
- The tests take too long to run.  They may need to be  
  built in release mode.  
  
In Christ,  
Steven Watanabe

---

## Comment 10

> Username: justinasvd  
> Created_at: 2018-02-26 16:59:21 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-368571500  

Okay, I will check that discard overflows behave as I imagine they should.

---

## Comment 11

> Username: justinasvd  
> Created_at: 2018-02-26 22:13:43 UTC  
> Updated_at: 2018-02-26 22:23:10 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-368669795  

Fixed excessive discard checks, which makes tests run reasonably fast now.  
  
Also added limit tests for discard. Everything seems to be fine, except I am not exactly sure what to do with wraparound. The principle of least surprise would dictate that it should throw. What do you think?

---

## Comment 12

> Username: swatanabe  
> Created_at: 2018-02-26 22:42:22 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-368677397  

AMDG  
  
On 02/26/2018 03:13 PM, Justinas V. Daugmaudis wrote:  
> Fixed excessive discard checks, which makes tests run reasonably fast now.  
>   
> Also added limit tests for discard.  
>   
  
I haven't actually tried this, but I suspect the  
test will fail if you multiply maxdiscard by 4 instead  
of 2 in test_max_discard.  (You're testing  
with 2 dimensions, which cancels out the multiply  
by 2, so the truncation in the assignment  
at qrng_base.hpp:178 won't trigger.)  
  
Also, the reason I uses 7-bits instead of  
8-bits when I suggested this is to make  
sure that the w parameter is respected.  
  
In Christ,  
Steven Watanabe

---

## Comment 13

> Username: justinasvd  
> Created_at: 2018-02-26 23:35:18 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-368690168  

Ok, now I see your point. Will fix the tests sometime tomorrow. As of now, I added additional checks for bit count overflows.

---

## Comment 14

> Username: justinasvd  
> Created_at: 2018-02-27 16:36:47 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-368940877  

@swatanabe More tests – done. Moreover, the code in discard_vector had a genuine bug, I reckon. The test now expects an exception in case of overflows in discard.

---

## Comment 15

> Username: justinasvd  
> Created_at: 2018-03-05 11:47:16 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-370394956  

1. Documentation is complete. But it would be good to review/correct wording. @NAThompson ?  
2. Stress-tested the generators with various funny types and bit length combinations, e.g., char, 7u, etc. No undefined behavious, errors get properly reported. However, I would say that multiprecision lib should probably support unsigned short for msb and lsb. But this is not a limitation in the generators per se.  
3. All obvious potential overflow-related bugs in qrng_base were fixed. In particular, there was a corner case in == operator, when eng.discard(maxdiscard) == other.seed(0), i.e., because of overflow the engine at the very end of the sequence would be equal to the engine at the very start of the sequence.  
4. The code properly compiles and passes tests with -DBOOST_RANDOM_DOXYGEN. No invalid code in headers, even in documentation part.

---

## Review 16 [Commented]

> Username: NAThompson  
> Created_at: 2018-03-05 18:06:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/random/pull/37#pullrequestreview-101263694  

📁 doc/concepts.qbk

```diff
 203 |+ 
 204 |+ A quasi-random number generator is a __UniformRandomNumberGenerator which
 205 |+ provides a deterministic sequence of quasi-random numbers, based on some
```

> Username: NAThompson  
> Created_at: 2018-03-05 17:58:48 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172274152  

''a deterministic sequence of random vectors"

> Username: justinasvd  
> Created_at: 2018-03-05 18:35:04 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172284864  

Sounds kinda oxymoronic, no? I would suggest just to change "numbers" to "values" so that we don't inadvertently imply that qrngs work in R^1 only.

> Username: NAThompson  
> Created_at: 2018-03-05 18:41:46 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172286746  

Sorry, "a deterministic sequence of vectors"

---

📁 doc/concepts.qbk

```diff
 208 |+ number generator]. The "quasi" modifier is used to denote more clearly that the
 209 |+ values produced by such a generator are neither random nor pseudo-random, but
 210 |+ they form a low discrepancy sequence. The intuitive idea is that a low discrepancy
```

> Username: NAThompson  
> Created_at: 2018-03-05 18:00:59 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172274945  

The "quasi" modifier is used to indicate that the stream of vectors delivered by the generator form a low-discrepancy sequence.

> Username: justinasvd  
> Created_at: 2018-03-05 18:37:45 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172285692  

If we use "values" above, then this wording should be kept as it is. There is nothing wrong with it.

---

📁 doc/concepts.qbk

```diff
 216 |+ such as the quasi-Monte Carlo method their lower discrepancy is an important advantage.
 217 |+ 
 218 |+ [note Quasi-Monte Carlo method uses a low-discrepancy sequence such as the
```

> Username: NAThompson  
> Created_at: 2018-03-05 18:02:24 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172275440  

"The quasi-Monte Carlo method uses . . "

> Username: justinasvd  
> Created_at: 2018-03-05 18:36:38 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172285352  

Ok.

---

📁 doc/concepts.qbk

```diff
 222 |+ whereas the rate of convergence for the Monte Carlo method, which uses a pseudo-random sequence,
 223 |+ is '''<code>O(N<superscript>-0.5</superscript>)</code>'''.]
 224 |+ 
```

> Username: NAThompson  
> Created_at: 2018-03-05 18:04:38 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172276042  

Quasi-Monte Carlo integration converges roughly as '''<code>O(1/N)</code>''', whereas the convergence of naive Monte Carlo integration, which uses pseudo-random sequences, is only '''<code>O(N<superscript>-0.5</superscript>)</code>'''.]

> Username: justinasvd  
> Created_at: 2018-03-05 18:43:21 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172287198  

No. The suggestion is less precise. Consider what "converges roughly as O(1/N)" is supposed to mean?

> Username: NAThompson  
> Created_at: 2018-03-05 18:59:40 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172292097  

Maybe return to the correct estimate of O(log(N)^d/N), as the logarithmic factors dominate for achievable N and somewhat large d.


---

## Comment 17

> Username: NAThompson  
> Created_at: 2018-03-05 18:06:54 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-370509192  

Just a few typos fixed here. Where is the example code?

---

## Comment 18

> Username: justinasvd  
> Created_at: 2018-03-05 18:44:59 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-370520938  

Doxygenized comments are also documentation. Mostly, what's important are comments before *_engine classes and before default specializations for those engines.  
  
As for the example, what kind of example should I provide?

---

## Comment 19

> Username: NAThompson  
> Created_at: 2018-03-05 18:47:31 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-370521709  

I would just put should how the code is used and state the interface in the docs. See:   
  
http://www.boost.org/doc/libs/1_66_0/libs/math/doc/html/math_toolkit/gauss_kronrod.html

---

## Comment 20

> Username: NAThompson  
> Created_at: 2018-03-05 18:49:38 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-370522367  

Note that the background info on quasi-random sequences is not that important, since most people will navigate from the Wikipedia article on QRNGs to the boost docs, not the other way around.

---

## Comment 21

> Username: justinasvd  
> Created_at: 2018-03-05 18:59:34 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-370525500  

Hm, I can write a very short tutorial how to use a 4-dimensional qrng to uniformly cover an S^2 sphere. I would like to keep the tutorial to the length that is typical of Boost.Random tutorials.

---

## Comment 22

> Username: NAThompson  
> Created_at: 2018-03-05 19:00:49 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-370525911  

I think that would be excellent.

---

## Comment 23

> Username: justinasvd  
> Created_at: 2018-03-06 10:38:08 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-370738802  

@NAThompson  Example – done.

---

## Comment 24

> Username: NAThompson  
> Created_at: 2018-03-06 16:12:22 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-370834774  

Looks good to me! For my own reference, could you also add a quick note about how to generate the entire vector at once? In your example, you extracted each element of the pseudo-random vector in different calls, which may be awkward in very high dimension.

---

## Comment 25

> Username: justinasvd  
> Created_at: 2018-03-06 17:27:12 UTC  
> Updated_at: 2018-03-06 17:28:21 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-370860665  

This is what variate_generator is for. Plug-in the generator, the distribution and use std::generate(my_sample.begin(), my_sample.end(), gen());  
  
The added bonus is that it is not limited to querying a single s-dimensional vector. This way you can fetch 10 vectors, if the problem calls for it.

---

## Comment 26

> Username: swatanabe  
> Created_at: 2018-03-06 17:42:07 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-370865473  

AMDG  
  
On 03/06/2018 10:27 AM, Justinas V. Daugmaudis wrote:  
> This is what variate_generator is for. Plug-in the generator, the distribution and use std::generate(my_sample.begin(), my_sample.end(), gen());  
>   
  
variate_generator is obsolete.  Just use  
std::bind(distribution, std::ref(engine))  
or [&]() { return distribution(engine); }  
  
In Christ,  
Steven Watanabe

---

## Comment 27

> Username: justinasvd  
> Created_at: 2018-03-06 17:47:45 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-370867363  

Thank you for the correction. This depreciation is news to me, but totally makes sense.

---

## Comment 28

> Username: NAThompson  
> Created_at: 2018-03-06 18:38:13 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-370883390  

It appears that the idiomatic method of generating the entire vector is not clear even to us, which is why I think it should be added to the docs.  
  
It looks like everything is approaching its completed form. What are the final steps to complete this feature?

---

## Comment 29

> Username: justinasvd  
> Created_at: 2018-03-06 18:54:54 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-370888441  

This mammoth of the PR still needs to be thoroughly reviewed by Steven. I did my best to write the generators that do exactly as advertised, and I am fairly confident about the algorithmic side of things, but we still need to verify this.

---

## Review 30 [Commented]

> Username: swatanabe  
> Created_at: 2018-03-06 20:03:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/random/pull/37#pullrequestreview-101673442  

📁 include/boost/random/detail/gray_coded_qrng.hpp

```diff
  80 |+   //!Throws: nothing.
  81 |+   static BOOST_CONSTEXPR result_type max BOOST_PREVENT_MACRO_SUBSTITUTION ()
  82 |+   { return (std::numeric_limits<result_type>::max)(); }
```

> Username: swatanabe  
> Created_at: 2018-03-06 19:05:14 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172625492  

This doesn't respect bit_count.  Use boost::low_bits_mask_t (from boost/integer/integer_mask.hpp).

> Username: justinasvd  
> Created_at: 2018-03-06 20:47:43 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172654483  

Nice idea, but if I implement max() as   
{ return low_bits_mask_t<LatticeT::bit_count>::sig_bits; }  
  
then 8 bit discard test doesn't compile because boost/integer/integer_mask.hpp:66:5: error: left operand of shift expression ‘(-1 << 7)’ is negative [-fpermissive]  
     BOOST_STATIC_CONSTANT( least, sig_bits = (~( ~(least( 0u )) << Bits )) );  
  
Basically, least is resolved to char, and it gets negative.  
  
For the time being I would suggest to write this as (std::numeric_limits<result_type>::max)() >> (std::numeric_limits<IntType>::digits - LatticeT::bit_count).  
  
This will produce the result we want until this problem with integer_mask gets resolved.


📁 include/boost/random/detail/qrng_base.hpp

```diff
  32 |+ 
  33 |+ template <typename T>
  34 |+ inline bool are_close(T x, T y, T eps) // |x - y| <= eps
```

> Username: swatanabe  
> Created_at: 2018-03-06 19:21:59 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172630240  

This function is unnecessary.  The only place you're using this is to compare integers.

> Username: justinasvd  
> Created_at: 2018-03-06 21:16:20 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172662378  

I dislike this orphan function that is used once, too. But how would you suggest to compactly write the conjunctions in operator== without losing code flow?

---

📁 include/boost/random/detail/qrng_base.hpp

```diff
 144 |+     // produce the same sequence because the generator triple
 145 |+     // (D, S, D) is equivalent to (D, S + 1, 0), where D is dimension, S -- seq_count,
 146 |+     // and the last one is curr_elem.
```

> Username: swatanabe  
> Created_at: 2018-03-06 19:27:58 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172632159  

Would it make sense to normalize the representation?

> Username: justinasvd  
> Created_at: 2018-03-06 21:03:07 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172658692  

It is normalized. The difference between (D, S, D) and (D, S + 1, 0) is that the former has all the elements exhausted but the new state is not yet computed (because not yet requested), while the latter is a freshly computed S + 1 state (because it was requested).  
  
It is useful to maintain this difference, because otherwise if you do engine.discard(maxdiscard) and compute straight to state (D, last_state + 1, 0), you would be overdiscarding, you wouldn't have the equality between maxdiscard operator() invocations and discard(maxdiscard).

---

📁 include/boost/random/detail/qrng_base.hpp

```diff
 106 |+     // Sets up the proper position of the element-to-read
 107 |+     // curr_elem = carry + corr*dimension_value
 108 |+     curr_elem = carry ^ (-static_cast<std::size_t>(corr) & dimension_value);
```

> Username: swatanabe  
> Created_at: 2018-03-06 20:00:07 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172641549  

MSVC warns: C4146 unary minus operator applied to an unsigned type, result still unsigned.  (This should be safe to suppress).

> Username: justinasvd  
> Created_at: 2018-03-06 20:50:54 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172655336  

Yes, it is. g++ doesn't complain here at all.

---

📁 include/boost/random/detail/qrng_base.hpp

```diff
 210 |+ 
 211 |+     std::size_t tmp = curr_elem;
 212 |+     derived().seed(seq_count + z);
```

> Username: swatanabe  
> Created_at: 2018-03-06 20:02:08 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172642081  

Add an explicit cast to suppress warnings here.


📁 include/boost/random/faure.hpp

```diff
 264 |+ template<typename RealType, typename SeqSizeT, typename PrimeTable = default_faure_prime_table>
 265 |+ class faure_engine
 266 |+   : public detail::qrng_base<
```

> Username: swatanabe  
> Created_at: 2018-03-06 19:46:12 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172637575  

This has the unfortunate side-effect that boost::random::detail is an associated namespace of faure_engine.  I generally avoid putting internal base classes in namespace detail.  (You can also put qrng_base in its own namespace).

> Username: justinasvd  
> Created_at: 2018-03-06 21:56:43 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172673912  

I see, I see... Would it be acceptable to deepen the namespace to, say, detail::quasi::qrng_base?  
IIRC, ADL would get into effect only for the innermost enclosing namespaces, no?

> Username: justinasvd  
> Created_at: 2018-03-07 07:23:01 UTC  
> Updated_at: 2018-03-14 12:30:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#discussion_r172757939  

I think this is done now. I moved qrng_base, gray_coded_qrng from namespace detail to qrng_detail.


---

## Comment 31

> Username: swatanabe  
> Created_at: 2018-03-06 21:19:14 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-370931883  

AMDG  
  
On 03/06/2018 02:03 PM, Justinas V. Daugmaudis wrote:  
>   
> +    // Note that two generators with different seq_counts and curr_elems can  
> +    // produce the same sequence because the generator triple  
> +    // (D, S, D) is equivalent to (D, S + 1, 0), where D is dimension, S -- seq_count,  
> +    // and the last one is curr_elem.  
>   
> It is normalized. The difference between (D, S, D) and (D, S + 1, 0) is that the former has all the elements exhausted but the new state is not yet computed (because not yet requested), while the latter is a freshly computed S + 1 state (because it was requested).  
>   
> It is useful to maintain this difference, because otherwise if you do engine.discard(maxdiscard) and compute straight to state (D, last_state + 1, 0), you would be overdiscarding, you wouldn't have the equality between maxdiscard operator() invocations and discard(maxdiscard).  
>   
  
  Is there ever a reason to want zero?  You're discarding  
the zero vector, so setting the initial state to (D, 0, D)  
should be fine.  operator() also never leaves curr_elem  
as 0.  
  
In Christ,  
Steven Watanabe

---

## Comment 32

> Username: justinasvd  
> Created_at: 2018-03-06 21:26:46 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-370934150  

Yes, set engine.seed(0) and you will have (D, 0, 0). Also, engine.seed(1) will give (D, 1, 0), etc.

---

## Comment 33

> Username: swatanabe  
> Created_at: 2018-03-06 21:54:28 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-370942456  

AMDG  
  
On 03/06/2018 01:47 PM, Justinas V. Daugmaudis wrote:  
>   
> then 8 bit discard test doesn't compile because boost/integer/integer_mask.hpp:66:5: error: left operand of shift expression ‘(-1 << 7)’ is negative [-fpermissive]  
>      BOOST_STATIC_CONSTANT( least, sig_bits = (~( ~(least( 0u )) << Bits )) );  
>   
  
  This has been fixed in develop (by inserting casts).  
least is actually unsigned char.  The problem is the  
implicit promotion to int.  
  
> Basically, least is resolved to char, and it gets negative.  
>   
  
In Christ  
Steven Watanabe

---

## Comment 34

> Username: justinasvd  
> Created_at: 2018-03-06 22:03:25 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-370945031  

Yay, works and tests pass! Thanks for removing that max() computation ugliness.

---

## Comment 35

> Username: justinasvd  
> Created_at: 2018-03-07 07:57:43 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-371054654  

@swatanabe  Done. Please check.

---

## Comment 36

> Username: justinasvd  
> Created_at: 2018-03-13 10:31:44 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-372619301  

@swatanabe  Did you have a chance to take a look? Personally, I think that things could be improved by ensuring strong exception safety in `niederreiter_base2` and `sobol` lattices. We are resizing them anyway, so it would make sense to simply compute into the new memory and swap at the end. What would you suggest?

---

## Comment 37

> Username: swatanabe  
> Created_at: 2018-03-13 16:37:06 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-372731798  

AMDG  
  
On 03/13/2018 04:31 AM, Justinas V. Daugmaudis wrote:  
> @swatanabe  Did you have a chance to take a look? Personally, I think that things could be improved by ensuring strong exception safety in `niederreiter_base2` and `sobol` lattices. We are resizing them anyway, so it would make sense to simply compute into the new memory and swap at the end. What would you suggest?  
>   
  
resize is only called from the constructor (strong  
exception safety is irrelevant) and the istream  
operator (this is not exception safe and must be  
fixed.  It can currently leave the lattice in  
a state inconsistent with quasi_state, which can  
potentially lead to out-of-bounds indexes).  Hmm.  
Strong exception safety doesn't really cost anything  
extra, so you might as well provide the strong guarantee.  
  
In Christ,  
Steven Watanabe

---

## Comment 38

> Username: justinasvd  
> Created_at: 2018-03-13 16:52:38 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-372737298  

Done.

---

## Comment 39

> Username: NAThompson  
> Created_at: 2018-03-13 22:22:59 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-372839180  

Excellent work Justinas. Is this ready to be merged into develop? If so, that gives me the opportunity to work on the integrator and perhaps discover some bugs via usage before release into 1.68.

---

## Comment 40

> Username: justinasvd  
> Created_at: 2018-03-14 07:48:09 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-372932492  

I am confident the code works; seed, discard functions have no hidden corner cases. Thanks to Steven test coverage is much better now. For fun and giggles I instantiated niederreiter_base2_engine with <char, 7u>. It produced a normal sequence, albeit a very short one (as could be expected).  
  
At this point I don't know what improvements I could make. In the future, however, I would like to reuse `powm` from multiprecision, provided that `integer_modulus` could be disabled by passing, say, mpl::false_ instead of integral `c`. I would like to reuse the prime table from Boost.Math, if we can add functions not only to get the nth prime, but also a prime that would be a lower bound of `n` (upper bound of `n` would come for free at that point). Those would be useful functions in and of themselves.  
  
_Summa summarum_: the final say is Steven's and I know perfectly well that expert programmers have an excellent sense of intuition if something not quite kosher goes on in the code. This intuition has to be satisfied. By adding more tests, if need be.

---

## Comment 41

> Username: justinasvd  
> Created_at: 2018-03-23 17:22:03 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-375739878  

@swatanabe Any comments, further improvements, test suggestions?

---

## Comment 42

> Username: NAThompson  
> Created_at: 2018-04-18 14:09:05 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-382399554  

Now that 1.67 has been released, is there any chance this can be merged?

---

## Comment 43

> Username: swatanabe  
> Created_at: 2018-04-18 14:32:30 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-382407888  

AMDG  
  
On 04/18/2018 08:09 AM, Nick wrote:  
> Now that 1.67 has been released, is there any chance this can be merged?  
>   
  
I'll try to get to it this week.  
  
In Christ,  
Steven Watanabe

---

## Comment 44

> Username: justinasvd  
> Created_at: 2018-05-03 10:58:35 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-386258484  

@swatanabe Hi, Steven. Did you have a chance to take a look?

---

## Comment 45

> Username: swatanabe  
> Created_at: 2018-05-07 18:47:21 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-387164153  

AMDG  
  
On 05/03/2018 04:58 AM, Justinas V. Daugmaudis wrote:  
> @swatanabe Hi, Steven. Did you have a chance to take a look?  
>   
  
I've started merging it.  
pass: gcc-7.3.1, clang-5.0.1 (-std=c++03/11/14/17)  
fail: vc9, vc10 (Not high priority, but  
  it should only take minor tweaks to fix).  
pass: vc11, vc12, vc14, vc141  
  
In Christ,  
Steven Watanabe

---

## Comment 46

> Username: justinasvd  
> Created_at: 2018-05-07 19:26:44 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-387175668  

Oh, wonderful news! I assume that failures for vc9 and vc10 are pretty much the same?

---

## Comment 47

> Username: swatanabe  
> Created_at: 2018-05-12 15:48:04 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-388563936  

AMDG  
  
On 05/07/2018 01:26 PM, Justinas V. Daugmaudis wrote:  
> Oh, wonderful news! I assume that failures for vc9 and vc10 are pretty much the same?  
>   
  
Right.  I can fix it by adding parentheses.  
  
Additional changes  
- bit_xor is not in c++03 (vc9, not worth fixing)  
- suppress C4800 (int to bool conversion)  
- niederreiter_base2.hpp:79: C4146 (unary minus on unsigned.  
  Signed integer overflow is undefined behavior, so I rewrote  
  the loop to go from 0 to pb_degree)  
- faure.hpp: Add explicit casts to suppress C4244.  
- test_max_seed at test_qrng_function.cpp:148 doesn't  
  work for faure, because eng.max is 1.0 and has nothing  
  to do with the maximum seed.  Oh, I see that even though  
  it's instantiated for faure, it isn't actually used.  
  I'll leave this as is.  
  
In Christ,  
Steven Watanabe

---

## Comment 48

> Username: NAThompson  
> Created_at: 2018-05-13 02:46:27 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-388597048  

Great work guys; both of you put in a huge effort into making this happen. I'll get wikipedia updated so that people know that this feature exists and hopefully it'll be useful.

---

## Comment 49

> Username: justinasvd  
> Created_at: 2018-05-13 03:41:48 UTC  
> Url: https://github.com/boostorg/random/pull/37#issuecomment-388598791  

Thank you, Steven and Nick. Good work, indeed. Moreover, should the public uncover some bug that is unknown to us a this moment, I will certainly fix it. (I have a vested interest to see this code correct. I use it for my own simulations.)

---
