# #447 Enhanced gcc clang warnings [Merged]

> Username: ckormanyos  
> Created at: 2022-04-21 16:00:08 UTC  
> Updated at: 2022-04-24 08:32:20 UTC  
> Merged at: 2022-04-24 06:26:44 UTC  
> Closed at: 2022-04-24 06:26:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/447  

The purpose of this PR is to track handling enhanced GCC/clang warnings as described in #445

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2022-04-22 10:34:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/447#issuecomment-1106359549  

GHA now allows re-run of failed jobs. Re-runs went green on GHA.

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2022-04-22 10:38:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/447#issuecomment-1106362639  

The next batch of commits uses the simple test file to get warn-free for bin-float on:  
- GCC/VC on MinGW, Win*, WSL  
- clang++ on WSL still has 30 warns  
  
Lots of little steps to do. Many of them, in fact. But the method(s) seem feasible and standalone code(s) make this hardening now at least possible.  
  
If/when this goes green, do next:  
  
- Handle clang warns.  
- Go for warn-free on gmp-float, mpfr-float, dec-float.  
- Extend the test program(s) to cover more code instantiation.  
- Set up a companion CI system (maybe a repo) to go hunting for `-Werror`.  
  
Cc: @mborland and @jzmaddock and @NAThompson

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2022-04-22 13:04:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/447#issuecomment-1106494606  

> whatever  
  
I made a little repo [here](https://github.com/ckormanyos/boost_multiprecision_harden) which pulls in `Config` from the submodule, but carries its own (copied, not cloned) version of `Multiprecision` standalone.  
  
CI in this little repo makes one single test now, but will add a few more to instantiate more stuff as progressing...  
  
Inch forward in this repo, manually merge to the _real_ Boost submod. This kind of thing seems feasible and could also (much later) provide a playground for CodeSonar, Synopsis Coverity, LGTM if we ever decide to go that way.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2022-04-23 08:07:29 UTC  
> Updated_at: 2022-04-23 08:09:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/447#issuecomment-1107423973  

While the next batch of edits cycles I could use some advice.  
  
First off:  
- Some non-ASCII chars got into the slash-slash-slashes near the tops of four headers. I was unsure how they got there and wondered if anyone knows of a Git or MSVC edit setting known to cause this kind of thing.  
  
There are three warnings that I could not handle fixing by myself, or that I would like advice on.  
  
If you get a chance, could John (@jzmaddock) or Matt (@mborland) could you please review Some warnings below?  
  
- On [this line](https://github.com/boostorg/multiprecision/blob/e8ef5648f46fffd95ff5f45f19c20c82983d27f9/include/boost/multiprecision/cpp_bin_float.hpp#L411), clang [says](https://github.com/ckormanyos/boost_multiprecision_harden/runs/6138663779?check_suite_focus=true#step:4:22) warning: implicit conversion turns floating-point number into integer: `float` to `bool`. Since this is done in the presence of `std::is_floating_point<Float>`, I was thinking comparison to be greater than `numeric_limits<Float>::epsilon()` might be OK. I'd like to avoid comparison with literal floating-point zero as well.  
- On [this line](https://github.com/boostorg/multiprecision/blob/e8ef5648f46fffd95ff5f45f19c20c82983d27f9/include/boost/multiprecision/cpp_int/misc.hpp#L146), clang [says](https://github.com/ckormanyos/boost_multiprecision_harden/runs/6138663779?check_suite_focus=true#step:4:34) warning: implicit conversion changes signedness. I am having difficulty figuring out what type(s) `R` is and how (or if) to cast?  
- On exactly the [line that follows](https://github.com/boostorg/multiprecision/blob/e8ef5648f46fffd95ff5f45f19c20c82983d27f9/include/boost/multiprecision/cpp_int/misc.hpp#L147), clang [says](https://github.com/ckormanyos/boost_multiprecision_harden/runs/6138663779?check_suite_focus=true#step:4:64) warning: implicit conversion changes signedness and I'm again confused how/if to cast?

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2022-04-23 08:19:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/447#issuecomment-1107428232  

> On [this line](https://github.com/boostorg/multiprecision/blob/e8ef5648f46fffd95ff5f45f19c20c82983d27f9/include/boost/multiprecision/cpp_bin_float.hpp#L411), clang [says](https://github.com/ckormanyos/boost_multiprecision_harden/runs/6138663779?check_suite_focus=true#step:4:22) warning: implicit conversion turns floating-point number into integer: float to bool. Since this is done in the presence of std::is_floating_point<Float>, I was thinking comparison to be greater than numeric_limits<Float>::epsilon() might be OK. I'd like to avoid comparison with literal floating-point zero as well.  
  
Grrr, the code is correct as is - which is to say (implicitly) "f is not zero".  So if we change it would have to be to `f != 0`.  
  
>On [this line](https://github.com/boostorg/multiprecision/blob/e8ef5648f46fffd95ff5f45f19c20c82983d27f9/include/boost/multiprecision/cpp_int/misc.hpp#L146), clang [says](https://github.com/ckormanyos/boost_multiprecision_harden/runs/6138663779?check_suite_focus=true#step:4:34) warning: implicit conversion changes signedness. I am having difficulty figuring out what type(s) R is and how (or if) to cast?  
  
If you look at the enable_if above, R is any integral type (signed or unsigned.  I wonder though if I have the brackets in the wrong place here, what about:  
  
```  
static_cast<R>((backend.limbs()[i] & mask) << shift)  
```  
?  
  
>On exactly the [line that follows](https://github.com/boostorg/multiprecision/blob/e8ef5648f46fffd95ff5f45f19c20c82983d27f9/include/boost/multiprecision/cpp_int/misc.hpp#L147), clang [says](https://github.com/ckormanyos/boost_multiprecision_harden/runs/6138663779?check_suite_focus=true#step:4:64) warning: implicit conversion changes signedness and I'm again confused how/if to cast?  
  
Try removing the static_cast to R, it looks superfluous to me, we should just be checking the bits in the limb (in fact this one does look like a possible bug!).

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2022-04-23 12:16:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/447#issuecomment-1107462278  

Thanks John.  
  
> So if we change it would have to be to `f != 0`  
  
I decided to actually change and went with `f != static_cast<Float>(0.0F)`. There was anothe similar instance I found in `cpp_int.hpp` as well.  
  
> brackets in the wrong place here...  
  
> (in fact this one does look like a possible bug!)  
  
Let's call it a near-bug. i don't really think it's finding a nugget. Anyway, yes. So I went with some [refactoring](https://github.com/boostorg/multiprecision/blob/2c845465baccb7532573a3d92f8e8e238e563d21/include/boost/multiprecision/cpp_int/misc.hpp#L145) around those two lines.  
  
The next batch of changes are cycling. These (in total) handle quite a few (around 75) warnings that I can find locally on my test code and in my own CI of my wide-integer project. In this way, `cpp_dec_float.hpp` and `cpp_int.hpp` have been hardened to some extent. If CI runs through green, I'll commit this first rudimentary step to develop.

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2022-04-23 16:46:31 UTC  
> Updated_at: 2022-04-23 16:46:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/447#issuecomment-1107532692  

Hi @jzmaddock this PR addresses about 125 warnings (mostly `-Wconversion` and `-Wsign-conversion` in `cdd_bin_float.hpp`, `cpp_int.hpp` and several headers included by them.  
  
CI ran green, as did all my local tests and I found no performance influence.  
  
I'd like to merge these now as the first small step toward code hardning. When continuing, I'd expect a few hundred more similar edits in all of Multiprecision.  
  
Anyway, thoughts on this first step? I'd like to merge? OK by you?

---

## Review 8 [Commented]

> Username: jzmaddock  
> Created_at: 2022-04-23 17:30:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/447#pullrequestreview-951020193  

📁 include/boost/multiprecision/cpp_int.hpp

```diff
 235 |       constexpr data_type(limb_type i) noexcept : first(i) {}
 234 |-       constexpr data_type(signed_limb_type i) noexcept : first(i < 0 ? static_cast<limb_type>(boost::multiprecision::detail::unsigned_abs(i)) : i) {}
 236 |+       constexpr data_type(signed_limb_type i) noexcept : first(i < 0 ? static_cast<limb_type>(boost::multiprecision::detail::unsigned_abs(i)) : static_cast<limb_type>(i)) {}
```

> Username: jzmaddock  
> Created_at: 2022-04-23 17:30:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/447#discussion_r856943417  

I'm thinking we could simplify this, and just initialize with the result of `unsigned_abs(i)` which would handle both the `i < 0` check and the cast to limb_type for us?  
  
Same with the next one below.

> Username: ckormanyos  
> Created_at: 2022-04-23 18:35:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/447#discussion_r857000914  

Done


---

## Review 9 [Commented]

> Username: jzmaddock  
> Created_at: 2022-04-23 17:32:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/447#pullrequestreview-951020287  

📁 include/boost/multiprecision/cpp_int.hpp

```diff
 805 |    BOOST_MP_FORCEINLINE BOOST_MP_CXX14_CONSTEXPR cpp_int_base(signed_limb_type i) noexcept((Checked == unchecked))
 804 |-        : m_wrapper(limb_type(i < 0 ? static_cast<limb_type>(-static_cast<signed_double_limb_type>(i)) : i)), m_limbs(1)
 806 |+        : m_wrapper(i < 0 ? static_cast<limb_type>(-static_cast<signed_double_limb_type>(i)) : static_cast<limb_type>(i)),
```

> Username: jzmaddock  
> Created_at: 2022-04-23 17:32:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/447#discussion_r856943509  

This would be safer using `unsigned_abs` here as well.

> Username: ckormanyos  
> Created_at: 2022-04-23 18:35:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/447#discussion_r857000789  

Done


---

## Review 10 [Commented]

> Username: jzmaddock  
> Created_at: 2022-04-23 17:37:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/447#pullrequestreview-951020680  

📁 include/boost/multiprecision/cpp_int/misc.hpp

```diff
 215 |+                const std::ptrdiff_t left_shift_amount = static_cast<std::ptrdiff_t>(static_cast<std::ptrdiff_t>(cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1>::limb_bits) - bits_to_keep);
 216 |+ 
 217 |+                mask = static_cast<limb_type>(mask << left_shift_amount);
```

> Username: jzmaddock  
> Created_at: 2022-04-23 17:37:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/447#discussion_r856944017  

Can we really not use <<= here?  It's fine, but it's just harder to read...

> Username: ckormanyos  
> Created_at: 2022-04-23 18:35:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/447#discussion_r856999568  

> Can we really not use <<= here? It's fine, but it's just harder to read...  
  
Alas the verbosity of my lines is scaling with my increasing age. Sure. I can terse-ify.


---

## Comment 11

> Username: jzmaddock  
> Created_at: 2022-04-23 17:41:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/447#issuecomment-1107540916  

Other than some very minor quibbles, that all looks fine to me.

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2022-04-24 07:28:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/447#issuecomment-1107779717  

Thanks Chris!!

---

## Comment 13

> Username: ckormanyos  
> Created_at: 2022-04-24 08:32:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/447#issuecomment-1107790738  

Thank you as well, John.  
As a next course, it seems like the next priority is to take a look at `gmp_float` and `mpfr_float`.

---
