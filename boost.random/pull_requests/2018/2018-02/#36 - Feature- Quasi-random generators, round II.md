# #36 Feature: Quasi-random generators, round II [Closed]

> Username: justinasvd  
> Created at: 2018-02-15 15:21:52 UTC  
> Updated at: 2018-02-18 09:10:11 UTC  
> Closed at: 2018-02-18 09:10:11 UTC  
> Url: https://github.com/boostorg/random/pull/36  

1. Removed typedefs from boost namespace.  
2. Upped supported dimensions for both Sobol and Niederreited base 2 qrngs.  
3. Default sequence count is now 64 bit.  
4. Cleaned up the warnings in doc generation.

---

## Review 1 [Commented]

> Username: NAThompson  
> Created_at: 2018-02-15 16:09:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/random/pull/36#pullrequestreview-96899851  

📁 include/boost/random/detail/gray_coded_qrng_base.hpp

```diff
  78 |+     // This is the bit that changes in the Gray-code representation as
  79 |+     // the count is advanced.
  80 |+     update_quasi(multiprecision::detail::find_lsb(~cnt));
```

> Username: NAThompson  
> Created_at: 2018-02-15 15:56:08 UTC  
> Updated_at: 2018-02-17 19:49:06 UTC  
> Url: https://github.com/boostorg/random/pull/36#discussion_r168519184  

In `boost/multiprecision/integer.hpp`, there exists a function `lsb`, which returns the `detail::find_lsb`. Presumably it's better to not rely on the detail namespace in another repo if you can avoid it.


📁 include/boost/random/faure.hpp

```diff
  87 |+   // there is a twist that powm actually computes base^e % m,
  88 |+   // so for m we plug-in a fully set bitset, whose modulo will be no-op.
  89 |+   return multiprecision::powm(base, e, ~std::size_t());
```

> Username: NAThompson  
> Created_at: 2018-02-15 16:02:14 UTC  
> Updated_at: 2018-02-17 19:49:06 UTC  
> Url: https://github.com/boostorg/random/pull/36#discussion_r168521195  

Maybe my previous advice to use `powm` was incorrect, if you don't need to use the modulus operator. Do you think it'll compile down to a no-op? If it does, will it do the wrong thing on overflow? (I just feel like there should be some way for code reuse on this one in boost or the stdlib, maybe that's naive. . . )

> Username: justinasvd  
> Created_at: 2018-02-15 16:15:38 UTC  
> Updated_at: 2018-02-17 19:49:06 UTC  
> Url: https://github.com/boostorg/random/pull/36#discussion_r168525605  

I think it's possible to reuse code there, but it will require a bit of effort.  
  
`powm` could probably be implemented like this:  
  
```C++  
template <class I1, class I2, class I3>  
typename enable_if_c<is_integral<I1>::value && is_unsigned<I2>::value && is_integral<I3>::value, I1>::type  
   powm(const I1& a, I2 b, I3 c)  
{  
   typedef typename detail::double_integer<I1>::type double_type;  
  
   I1 x(1), y(a);  
   double_type result;  
  
   while(b > 0)  
   {  
      if(b & 1)  
      {  
         multiply(result, x, y);  
         x = integer_modulus(result, c);  
      }  
      multiply(result, y, y);  
      y = integer_modulus(result, c);  
      b >>= 1;  
   }  
   return integer_modulus(x, c);  
}  
```  
  
(Note `return integer_modulus(x, c);` instead of plain `return x % c;`)  
  
And if  integer_modulus would support mpl::false_() as the second argument, it could return just the first one. Which would make everything work.

> Username: justinasvd  
> Created_at: 2018-02-16 12:04:21 UTC  
> Updated_at: 2018-02-17 19:49:06 UTC  
> Url: https://github.com/boostorg/random/pull/36#discussion_r168739819  

I will restore my previous code here, but let's keep in minds that with the suggestion above code reuse would be possible.

---

📁 include/boost/random/faure.hpp

```diff
 230 |+   // which happens relatively rarely.
 231 |+   std::vector<packed_uint_t> coeff; // packed upper (!) triangular matrix
 232 |+   std::vector<RealType> ytemp;
```

> Username: NAThompson  
> Created_at: 2018-02-15 16:04:51 UTC  
> Updated_at: 2018-02-17 19:49:06 UTC  
> Url: https://github.com/boostorg/random/pull/36#discussion_r168522023  

Does @swatanabe have a convention for member variables to follow, like `m_` prefixes?


📁 include/boost/random/niederreiter_base2.hpp

```diff
 162 |+       }
 163 |+ 
 164 |+       const unsigned degree = multiprecision::detail::find_msb(poly); // integer log2(poly)
```

> Username: NAThompson  
> Created_at: 2018-02-15 16:07:39 UTC  
> Updated_at: 2018-02-17 19:49:06 UTC  
> Url: https://github.com/boostorg/random/pull/36#discussion_r168522906  

`boost/multiprecision/integer.hpp` has `msb`, which returns `detail::find_msb`

> Username: justinasvd  
> Created_at: 2018-02-15 16:21:45 UTC  
> Updated_at: 2018-02-17 19:49:06 UTC  
> Url: https://github.com/boostorg/random/pull/36#discussion_r168527651  

Done.


---

## Comment 2

> Username: justinasvd  
> Created_at: 2018-02-15 16:29:17 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-365982092  

Mostly, I am a bit concerned that Sobol tables could be excessively large. sobol_data.hpp is now 1.7 Mb, which is a bit hefty for an include, no?

---

## Comment 3

> Username: NAThompson  
> Created_at: 2018-02-15 16:47:26 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-365988309  

Yes, that could be a problem. Is there an algorithm to rapidly generate the direction numbers of the Sobol sequence at runtime? This would be more like what is done in boost.math: If you request more precision from (say) `boost::math::constants::pi<Real>()` than is cached, then an algorithm computes it. Otherwise, it just returns the number stored in the include file.  
  
The `boost.math` prime table has 10,000 elements, and too my knowledge no one complains about it. But you are correct that 1.7mb is pretty big and should be avoided if possible.  
  
Could you run a `time g++ sobol_test.cpp` and see if the compile time is unbearable?

---

## Comment 4

> Username: justinasvd  
> Created_at: 2018-02-15 17:55:03 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366008844  

time g++ faure_validate.cpp -O3  
real	0m2,911s  
user	0m2,717s  
sys	0m0,193s  
  
time g++ niederreiter_base2_validade.cpp -O3  
real	0m3,571s  
user	0m3,365s  
sys	0m0,205s  
  
time g++ sobol_validate.cpp -O3  
real	0m4,311s  
user	0m4,135s  
sys	0m0,175s  
  
Doesn't seem that the compile time is unbearable, but Sobol test certainly compiles slower than the lightweight (in comparison) Faure.

---

## Comment 5

> Username: swatanabe  
> Created_at: 2018-02-15 18:21:16 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366016299  

AMDG  
  
On 02/15/2018 09:29 AM, Justinas V. Daugmaudis wrote:  
> Mostly, I am a bit concerned that Sobol tables could be excessively large. sobol_data.hpp is now 1.7 Mb, which is a bit hefty for an include, no?  
>   
  
  It's too much.  Please also consider that this  
will bloat object files and executables.  If you  
really need to have all this data static, then  
at least make it separately compiled.  
  
In Christ,  
Steven Watanabe

---

## Comment 6

> Username: swatanabe  
> Created_at: 2018-02-15 18:29:40 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366018585  

AMDG  
  
On 02/15/2018 09:09 AM, Nick wrote:  
> NAThompson commented on this pull request.  
>   
> <snip>  
> +inline std::size_t integer_pow(std::size_t base, std::size_t e)  
> +{  
> +  // Reuse the implementation of exponentiation by squaring, but  
> +  // there is a twist that powm actually computes base^e % m,  
> +  // so for m we plug-in a fully set bitset, whose modulo will be no-op.  
> +  return multiprecision::powm(base, e, ~std::size_t());  
>   
> Maybe my previous advice to use `powm` was incorrect, if you don't need to use the modulus operator. Do you think it'll compile down to a no-op? If it does, will it do the wrong thing on overflow?  
  
Overflow is guaranteed to wrap modulo 2^k for unsigned types.  
  
> (I just feel like there should be some way for code reuse on this one in boost or the stdlib, maybe that's naive. . . )  
>   
  
  This has got to be wrong.  The correct modulus is  
std::numeric_limits<size_t>::max() + 1, which is  
not representable in a size_t.  Making the modulus  
too small by one, will cause the result to be 0  
whenever it should be 0xFFFFFFFFFFFFFFFF.  
  
In Christ,  
Steven Watanabe

---

## Comment 7

> Username: swatanabe  
> Created_at: 2018-02-15 18:35:59 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366020357  

AMDG  
  
On 02/15/2018 08:21 AM, Justinas V. Daugmaudis wrote:  
> 1. Removed typedefs from boost namespace.  
> 3. Default sequence count is now 64 bit.  
  
Upon further reflection, I think the correct way is:  
  
template<class UIntType, int w>  
class sobol_engine;  
  
typedef sobol_engine<boost::uint_least32_t, 32> sobol32;  
typedef sobol_engine<boost::uint_least64_t, 64> sobol64;  
  
In Christ,  
Steven Watanabe

---

## Comment 8

> Username: justinasvd  
> Created_at: 2018-02-15 18:41:14 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366021958  

Looks nice to me. But what's the purpose of template w parameter? How would it be used?

---

## Comment 9

> Username: swatanabe  
> Created_at: 2018-02-15 19:13:06 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366030996  

AMDG  
  
On 02/15/2018 11:41 AM, Justinas V. Daugmaudis wrote:  
> Looks nice to me. But what's the purpose of template w parameter? How would it be used?  
>   
  
  It indicates the bit width.  This is the convention  
that the standard library uses, because the standard  
does not guarantee that there are integer types of  
any specific width.  It only guarantees minimum sizes.  
Thus, uint_least32_t is always defined, but uint32_t  
might not be.  
  
In Christ,  
Steven Watanabe

---

## Comment 10

> Username: justinasvd  
> Created_at: 2018-02-15 19:28:49 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366035558  

Okay, then for niederreiter_base2 and sobol this <int w> param should be used, then.  
It will be used for the Gray code bits and for the counter (seq_count).  
  
Thus we will have:  
typedef sobol_engine<32> sobol32;  
typedef sobol_engine<64> sobol64;  
  
typedef niederreiter_base2_engine<32> niederreiter_base2_32;  
typedef niederreiter_base2_engine<64> niederreiter_base2_64;  
  
Then faure_engine needs to be parametrized as RealType and int w.  
typedef faure_engine<float, 32> faure32;  
typedef faure_engine<double, 64> faure64;  
  
Something like this. What do you think?

---

## Comment 11

> Username: justinasvd  
> Created_at: 2018-02-15 19:49:01 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366041127  

@NAThompson Generating Sobol data for requested dimension is an absolute no. It is simply an overkill. Especially considering that we would have to maintain and update the solution with respect to the newest research in that area.  
  
The best that I can think of is something like this:  
1. you have some awesome table you want to have;  
2. you pass this table to the generator.  
Otherwise, the default reasonably-sized tables will be used.  
  
I think that for niederreiter_base2 4720 dimensions is more than people have seen in a typical implementation. It's still a table of just 4720 unsigned shorts and thus bearable.  
  
For Sobol I think I will downgrade to some dimension where numbers fit to unsigned short as well. Most likely I will simply cut off the newest 21201 dim tables to some reasonable size.  
  
For aficionados and people pushing to the extremes I will simply provide a constructor argument to pass the table, and it will be their own responsibility from there. Sounds ok?

---

## Comment 12

> Username: swatanabe  
> Created_at: 2018-02-15 20:09:12 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366046555  

AMDG  
  
On 02/15/2018 12:28 PM, Justinas V. Daugmaudis wrote:  
> <snip>  
> Then faure_engine needs to be parametrized as RealType and int w.  
> typedef faure_engine<float, 32> faure32;  
> typedef faure_engine<double, 64> faure64;  
>   
  
  That's not quite correct, as float and double only  
have 24 and 53 bits in the mantissa, respectively.  
It looks to me like faure is fundamentally floating  
point rather than being an integer algorithm that's  
been written using floating point numbers (like  
lagged_fibonacci_01 or ranlux_01).  Thus, I don't  
think the bit width actually makes sense.  w for  
floating point generators only makes sense if you  
could easily rewrite the algorithm to generate  
the same sequence * 2^w as integers.  
  
> Something like this. What do you think?  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 13

> Username: justinasvd  
> Created_at: 2018-02-15 20:40:06 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366054627  

Sobol tables are down to 230Kb, yet they still support respectable 3667 dimensions. I think it's a nice balance.

---

## Comment 14

> Username: justinasvd  
> Created_at: 2018-02-16 12:17:28 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366220933  

@NAThompson Now you can use whatever tables you want. I also made the tables part of the type definition, since otherwise generator comparison would not make sense.

---

## Review 15 [Commented]

> Username: NAThompson  
> Created_at: 2018-02-16 16:15:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/random/pull/36#pullrequestreview-97231841  

📁 doc/concepts.qbk

```diff
 218 |+ [note Quasi-random sequences are known to give efficient numerical integration
 219 |+ rules in many Bayesian statistical problems where the posterior distribution can be
 220 | transformed into periodic functions on a hypercube.]
```

> Username: NAThompson  
> Created_at: 2018-02-16 16:15:47 UTC  
> Updated_at: 2018-02-17 19:49:06 UTC  
> Url: https://github.com/boostorg/random/pull/36#discussion_r168800784  

I do not believe there is any requirement that the function must be periodic.

> Username: justinasvd  
> Created_at: 2018-02-16 17:40:47 UTC  
> Updated_at: 2018-02-17 19:49:06 UTC  
> Url: https://github.com/boostorg/random/pull/36#discussion_r168823423  

I didn't imply that periodicity was a requirement. I just meant that QMC numerical integration is notably efficient there. But I think that we can broaden the description and make it less technical.   
  
For example:  
Quasi-Monte Carlo method uses a low-discrepancy sequence such as the Niederreiter base 2 sequence, the Sobol sequence, or the Faure sequence among the others. The advantage of using low-discrepancy sequences is a probabilistically faster rate of convergence. Quasi-Monte Carlo has a rate of convergence around to O(1/N), whereas the rate of convergence for the Monte Carlo method, which uses a pseudo-random sequence,  is O(N^−(1/2)).

> Username: NAThompson  
> Created_at: 2018-02-16 18:01:53 UTC  
> Updated_at: 2018-02-17 19:49:06 UTC  
> Url: https://github.com/boostorg/random/pull/36#discussion_r168828373  

Oh, I didn't know that there was a better convergence rate for periodic integrands using qmc. If you do add this to the docs, make sure to get the logarithmic factors on the convergence: O(log(N)^d/N).

> Username: justinasvd  
> Created_at: 2018-02-16 18:52:58 UTC  
> Updated_at: 2018-02-17 19:49:06 UTC  
> Url: https://github.com/boostorg/random/pull/36#discussion_r168840676  

Nah, rewrote the note like this  
  
[note Quasi-Monte Carlo method uses a low-discrepancy sequence such as the   
Niederreiter base 2 sequence, the Sobol sequence, or the Faure sequence among the others.   
The advantage of using low-discrepancy sequences is a probabilistically faster rate of   
convergence. Quasi-Monte Carlo has a rate of convergence close to '''<code>O(1/N)</code>''',   
whereas the rate of convergence for the Monte Carlo method, which uses a pseudo-random sequence,   
is '''<code>O(N<superscript>-0.5</superscript>)</code>'''.]   
  
I think "close to" is enough here.


---

## Comment 16

> Username: NAThompson  
> Created_at: 2018-02-16 16:18:59 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366282167  

Cool, do you have an account on codereview.stackexchange.com? If not, do you mind if I communicate this code there on your behalf?

---

## Comment 17

> Username: justinasvd  
> Created_at: 2018-02-16 17:28:17 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366302452  

Nope, I don't have an account, and nope, I don't mind if anybody else reviews the code. The more eyes, the merrier.

---

## Comment 18

> Username: NAThompson  
> Created_at: 2018-02-16 17:55:19 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366309992  

Ok, here's the link:  
  
[https://codereview.stackexchange.com/questions/187712/quasi-random-number-generators](https://codereview.stackexchange.com/questions/187712/quasi-random-number-generators)  
  
Hopefully we get some good feedback; it is often the case that people give very nice feedback there, but this is a technically sophisticated effort and might be too much effort for the members of that forum.

---

## Comment 19

> Username: justinasvd  
> Created_at: 2018-02-16 18:03:02 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366311973  

Seems good.  
  
BTW, what did you think about the solution for tables? I hope we reached some middle ground between general usefulness and hardcore high-dimensionality.

---

## Comment 20

> Username: NAThompson  
> Created_at: 2018-02-16 18:04:10 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366312264  

I think the solution for the tables is good. Could you link the 21000 dimensional tables in the docs?  
  
Also, try to link to wayback machine, these academic web pages disappear all the time.

---

## Comment 21

> Username: justinasvd  
> Created_at: 2018-02-16 19:40:48 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366338151  

Done.

---

## Comment 22

> Username: NAThompson  
> Created_at: 2018-02-17 16:14:56 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366451644  

Ok, so just to regroup:   
  
- We've verified that this code is fast  
- We've verified is has no memory corruptions or undefined behavior  
- We've verified that this is better than existing open source alternatives, due to features and much higher dimensionality  
- We have a use case which will consume this feature as soon as it is merged  
  
But:  
  
- The docs do not show an example. I don't think anyone goes to boost docs to learn about qrngs, they'll migrate from wikipedia and want to see a code example immediately. (Note to self: Need to edit wikipedia to link to these once they drop.)  
- We do not have a unit test showing that the L2 discrepancy of the sequences satisfy known bounds. My own opinion is that this is a stronger test than matching published data, but this is not a showstopper for me.  
- We still have a too-small prime table, and the prime table in boost.math doesn't seem to offer you the features you need. I'll talk to John Maddock about merging a sieve of Eratosthenes into boost.integer, which will hopefully solve this problem.  
- There is no discussion on how to apply randomization to the sequences. I think there should be either a recommendation on how to do this in the docs, or a very simple Cranley-Patterson rotation should be supplied in the code. I have a Cranley-Patterson rotation [here](https://github.com/boostorg/math/blob/9d4752bc5671fad664f311f23fccbf49ec509b45/include/boost/math/tools/cranley_patterson_rotation.hpp) but I don't think it supports the typical idioms required in boost.random.  
  
These are small problems. I still think that these will be the best feature landing in 1.67.

---

## Comment 23

> Username: justinasvd  
> Created_at: 2018-02-17 17:11:51 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366455710  

Thank you for your nice summary.  
  
I just wanted to add a few words about discrepancy testing. You see, I have implemented an algorithm to compute upper bounds for the star discrepancy of an arbitrary set of n points in the s-dimensional unit cube as proposed in Eric Thiemard's paper: "Computing bounds for the star discrepancy", Computing  65 (2000) 2, 169-186.  
  
I just didn't add this to this PR for several reasons:  
  
1. This PR is big enough as it is. The d_star code itself is complex (~20 kbs of just dense code to review).  
2. Furthermore, this raises the question: does discrepancy measuring work?  
3. This leads to a convoluted scheme: a) verify discrepancy tester itself (with, say, a known Halton sequence of known bounds); b) then use the verifier to verify, say, niederreiter_base2. It should get the expected discrepancy of D_n^*(x) = O( (log n)^s∕n ) within some reasonable given epsilon.  
4. Then we need to select a trio of parameters: number of points N, dimension s and epsilon eps for discrepancy tests. And this is not as straightforward as it seems, _especially_ if you want to run tests as part of automated test suite. You see, without careful fiddling those tests would be – more likely than not – exceedingly slow, but then this "careful fiddling" is fishy in the end.   
  
This is my rationale for not submitting that code here. Matching published data is a strong enough guarantee. It's fast, too.

---

## Comment 24

> Username: NAThompson  
> Created_at: 2018-02-17 19:22:31 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366464995  

That sounds reasonable to me! It appears @swatanabe is busy with the Yap review, but we can hope he has time to merge before 1.67 closes.

---

## Comment 25

> Username: swatanabe  
> Created_at: 2018-02-18 00:03:04 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366480969  

AMDG  
  
On 02/15/2018 08:21 AM, Justinas V. Daugmaudis wrote:  
>   
>     A test/niederreiter_base2_validade.cpp (372)  
  
The file name is misspelled.  
  
In Christ,  
Steven Watanabe

---

## Comment 26

> Username: swatanabe  
> Created_at: 2018-02-18 01:14:14 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366484489  

AMDG  
  
  This PR needs to be rebased against develop.  
multiprecision_float_test currently fails because  
of changes to Boost.Test.  
  
In Christ,  
Steven Watanabe

---

## Comment 27

> Username: justinasvd  
> Created_at: 2018-02-18 09:10:11 UTC  
> Url: https://github.com/boostorg/random/pull/36#issuecomment-366502233  

Will do. File rename and PR reopen on develop branch.

---
