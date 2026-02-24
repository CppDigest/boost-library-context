# #373 Remove dependencies from cpp_int_config [Merged]

> Username: mborland  
> Created at: 2021-10-02 09:48:49 UTC  
> Updated at: 2021-10-06 18:11:40 UTC  
> Merged at: 2021-10-06 07:21:36 UTC  
> Closed at: 2021-10-06 07:21:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/373  

Removes uses of boost.assert, and boost.integer. Also implements `BOOST_MP_STANDALONE` to continue work on issue #364.

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2021-10-02 12:21:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/373#pullrequestreview-769490731  

📁 include/boost/multiprecision/cpp_int/cpp_int_config.hpp

```diff
  86 | using limb_type = detail::largest_unsigned_type<64>::type;
  61 |- using signed_limb_type = detail::largest_signed_type<64>::type  ;
  62 |- using double_limb_type = boost::uint128_type                    ;
```

> Username: jzmaddock  
> Created_at: 2021-10-02 12:21:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/373#discussion_r720672738  

Using these typedefs suppresses endless warnings from the compiler about using a non-std extension - perhaps we can lift that logic from Boost.Config?

> Username: mborland  
> Created_at: 2021-10-02 12:34:11 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/373#discussion_r720674112  

That should be easy enough. I think I need to define `BOOST_HAS_INT128` in a standalone config like math has.


---

## Comment 2

> Username: mborland  
> Created_at: 2021-10-03 13:27:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/373#issuecomment-932951346  

@jzmaddock The only test failure was a timeout. Should be good pending further review.

---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2021-10-04 11:38:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/373#pullrequestreview-770180275  

📁 include/boost/multiprecision/cpp_int/cpp_int_config.hpp

```diff
  35 |+                  typename std::conditional<Bits == sizeof(short) * CHAR_BIT, short,
  36 |+                  typename std::conditional<Bits == sizeof(int) * CHAR_BIT, int,
  37 |+                  typename std::conditional<Bits == sizeof(long) * CHAR_BIT, long, long long
```

> Username: jzmaddock  
> Created_at: 2021-10-04 11:38:11 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/373#discussion_r721279608  

Out of an over-abundance of caution, there's just a couple more things we should do here I think.  
  
In this case, before setting `type` to `[unsigned] long long`, we should check that the bit count matches up, in the case that we have no matching type, maybe the best we can do is set exact to to type `void`, any better ideas?


---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2021-10-04 11:40:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/373#pullrequestreview-770182124  

📁 include/boost/multiprecision/detail/assert.hpp

```diff
  19 |+ #define BOOST_MP_ASSERT(expr) BOOST_ASSERT(expr)
  20 |+ #define BOOST_MP_ASSERT_MSG(expr, msg) BOOST_ASSERT_MSG(expr, msg)
  21 |+ #define BOOST_MP_STATIC_ASSERT(expr) BOOST_STATIC_ASSERT(expr)
```

> Username: jzmaddock  
> Created_at: 2021-10-04 11:40:20 UTC  
> Updated_at: 2021-10-04 11:40:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/373#discussion_r721281100  

I don't think these are in use any more - can we check and maybe remove all references to BOOST_STATIC_ASSERT?


---

## Review 5 [Commented]

> Username: jzmaddock  
> Created_at: 2021-10-04 12:23:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/373#pullrequestreview-770223347  

📁 include/boost/multiprecision/detail/standalone_config.hpp

```diff
  45 |+ 
  46 |+ #if defined(BOOST_HAS_INT128) && defined(__cplusplus)
  47 |+ namespace boost{
```

> Username: jzmaddock  
> Created_at: 2021-10-04 12:23:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/373#discussion_r721311494  

I think we should avoid putting these directly in namespace boost: imagine 2 different Boost libraries doing the same thing.  Suggest we put these directly into boost::multiprecision here, and remove the definitions in cpp_int_config.hpp (or vice versa?)


---

## Review 6 [Commented]

> Username: jzmaddock  
> Created_at: 2021-10-04 12:27:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/373#pullrequestreview-770227175  

📁 include/boost/multiprecision/detail/standalone_config.hpp

```diff
  36 |+ // int/float 128 types
  37 |+ 
  38 |+ #ifdef (__SIZEOF_INT128__)
```

> Username: jzmaddock  
> Created_at: 2021-10-04 12:27:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/373#discussion_r721314342  

This will more or less work for vanilla GCC, but if you look at Boost.Config the ways in which this gets defined (or undefined) are pretty complex.  Not sure if we need to worry too much - maybe the answer is "use Boost" if the configuration is wrong... only SNAFU is that everything may compile but be inefficient if __int128 wasn't enabled.


---

## Comment 7

> Username: jzmaddock  
> Created_at: 2021-10-04 14:31:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/373#issuecomment-933545437  

@NAThompson , @ckormanyos ,   
  
Just thinking out loud here, but where are we going with standalone usage?  
  
For example: a user just needing mpz_int probably has very few dependencies already, someone using cpp_Int might reasonably be required to have Boost.Config available, and for tgamma with cpp_bin_float we would need Boost.Math as well.  So one definition of "standalone" might be zero dependencies unless you use feature "X" in which case library "Y" is required.  
  
So we could:  
  
* For "difficult" configuration decisions, require Boost.Cong, this would be a #error along with a helpful message if not available.  
* For functions implemented by Boost.Math we would make instantiating those functions generate static_assert's unless the Math library is available.  
  
What do you all think?  Am I over-thinking this?

---

## Comment 8

> Username: NAThompson  
> Created_at: 2021-10-04 15:53:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/373#issuecomment-933620755  

> Just thinking out loud here, but where are we going with standalone usage?  
  
Is it possible to do standalone config + multiprecision? I think nobody is gonna complain about that dependency set.

---

## Comment 9

> Username: mborland  
> Created_at: 2021-10-04 16:29:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/373#issuecomment-933651465  

> > Just thinking out loud here, but where are we going with standalone usage?  
>   
> Is it possible to do standalone config + multiprecision? I think nobody is gonna complain about that dependency set.  
  
config + multiprecision wouldn't be overly difficult since config currently has no dependencies. I like @jzmaddock suggestion of offering vanilla (e.g. x86_64 + GCC + Linux) config, but then something more difficult (e.g. z/Architecture) requiring config. Would this fit your use case @NAThompson?

---

## Comment 10

> Username: NAThompson  
> Created_at: 2021-10-04 16:43:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/373#issuecomment-933662654  

> Would this fit your use case @NAThompson?  
  
Yes. Actually, a config + multiprecision would be fine as well.

---

## Comment 11

> Username: mborland  
> Created_at: 2021-10-04 17:26:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/373#issuecomment-933696845  

> Yes. Actually, a config + multiprecision would be fine as well.  
  
Since config is already dependency free is it possible to add it a submodule/link like the [Microsoft STL has for Boost.Math](https://github.com/microsoft/STL)? That would make packaging the dependency easy, and should be totally transparent to the end user.

---

## Comment 12

> Username: NAThompson  
> Created_at: 2021-10-04 17:44:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/373#issuecomment-933710122  

> Since config is already dependency free is it possible to add it a submodule/link like the Microsoft STL has for Boost.Math?   
  
That's a good idea, IMO. I'm not quite sure how the mechanics work, esp. if the entire boost is also on the syspath.

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2021-10-04 17:54:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/373#issuecomment-933717106  

>Since config is already dependency free is it possible to add it a submodule/link like the Microsoft STL has for Boost.Math? That would make packaging the dependency easy, and should be totally transparent to the end user.  
  
I don't think we can formally add it as a git submodule, but we could package it up as part of the download if we do independent releases?

---

## Comment 14

> Username: mborland  
> Created_at: 2021-10-04 18:01:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/373#issuecomment-933722337  

> I don't think we can formally add it as a git submodule, but we could package it up as part of the download if we do independent releases?  
  
That makes sense too. Offer something like a tarball, a `.deb`, and a `.rpm`?

---

## Comment 15

> Username: NAThompson  
> Created_at: 2021-10-04 18:58:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/373#issuecomment-933764928  

>  Offer something like a tarball, a .deb, and a .rpm?  
  
Github by default zips up your code into a zip file. I think we'd need to prepare a zip file with both config and multiprecision, then make sure config is properly hidden with a `CMakeLists.txt` which is configured correctly and doesn't break the main project.  
  
I imagine this will be 10 lines of code, but extremely difficult to get right.

---

## Comment 16

> Username: mborland  
> Created_at: 2021-10-05 17:04:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/373#issuecomment-934595736  

This latest iteration is clean. The only CI failure is a timeout.

---
