# #53 Refactoring of random provider [Closed]

> Username: jeking3  
> Created at: 2017-11-08 21:42:19 UTC  
> Updated at: 2018-06-02 23:00:07 UTC  
> Closed at: 2017-12-18 14:59:30 UTC  
> Url: https://github.com/boostorg/uuid/pull/53  

Changed the default random_generator implementation to use  
operating-system provided entropy as it is more secure and  
faster for the typical use case of generating one uuid at  
a time.  
  
This is a breaking change for anyone passing a mt19937  
into one of the explicit constructors of random_generator,  
which would be quite rare.  
  
Changed the default random provider on Windows to use BCrypt  
where available, falling back to Wincrypt when necessary or  
when explicitly requested through a macro.  
  
Provide a new random_generator_mt19937 type definition for  
use cases where a large number of uuids need to be created  
with high performance.  This is equivalent to the previous  
definition of random_generator.  
  
Provide a random generation benchmark test showing the  
cutoff where the mt19937-based generator will outperform the  
standard generator based on wall time.  
  
Removed template specialization for boost::random::random_device  
so that any UniformRandomNumberGenerator can be used properly  
with random_generator.  
  
Replaced the seed_rng detail implementation (which had a number  
of flaws) with a replacement header-only random_provider  
implementation.  
  
Note: entropy generation errors will cause an entropy_error  
to be thrown from random_generator.  The previous implementation  
ignored errors and silently failed.  
  
Added internal support for entropy generation on cloudabi  
platform leveraging the new random_device implementation.  
  
Added internal support for Universal Windows Platform (UWP)  
development leveraging the new random_device implementation.  
  
Added internal support for getentropy() on Linux and OpenBSD  
if certain requirements are met.  
  
This is an evolution of #52  
This fixes #24

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-11-08 21:58:59 UTC  
> Updated_at: 2017-11-10 22:40:39 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-342974653  

# [Codecov](https://codecov.io/gh/boostorg/uuid/pull/53?src=pr&el=h1) Report  
> Merging [#53](https://codecov.io/gh/boostorg/uuid/pull/53?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/uuid/commit/5a48cab9af5b522c6f4851b7312761493e2e4c78?src=pr&el=desc) will **decrease** coverage by `0.21%`.  
> The diff coverage is `98.14%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/uuid/pull/53/graphs/tree.svg?height=150&token=6falIr5npV&width=650&src=pr)](https://codecov.io/gh/boostorg/uuid/pull/53?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #53      +/-   ##  
===========================================  
- Coverage    97.41%   97.19%   -0.22%       
===========================================  
  Files           11       13       +2       
  Lines          619      607      -12       
===========================================  
- Hits           603      590      -13       
- Misses          16       17       +1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/uuid/pull/53?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/uuid/basic\_name\_generator.hpp](https://codecov.io/gh/boostorg/uuid/pull/53?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2Jhc2ljX25hbWVfZ2VuZXJhdG9yLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/uuid/random\_generator.hpp](https://codecov.io/gh/boostorg/uuid/pull/53?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL3JhbmRvbV9nZW5lcmF0b3IuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/uuid/entropy\_error.hpp](https://codecov.io/gh/boostorg/uuid/pull/53?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2VudHJvcHlfZXJyb3IuaHBw) | `100% <100%> (ø)` | |  
| [include/boost/uuid/detail/random\_provider.hpp](https://codecov.io/gh/boostorg/uuid/pull/53?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC9yYW5kb21fcHJvdmlkZXIuaHBw) | `100% <100%> (ø)` | |  
| [...nclude/boost/uuid/detail/random\_provider\_posix.ipp](https://codecov.io/gh/boostorg/uuid/pull/53?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC9yYW5kb21fcHJvdmlkZXJfcG9zaXguaXBw) | `96% <96%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/uuid/pull/53?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/uuid/pull/53?src=pr&el=footer). Last update [5a48cab...b27755e](https://codecov.io/gh/boostorg/uuid/pull/53?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2017-11-08 22:29:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75272849  

📁 include/boost/uuid/detail/random_provider.hpp

```diff
  42 |+     void generate(Iter first, Iter last)
  43 |+     {
  44 |+         BOOST_STATIC_ASSERT(sizeof(Iter) >= sizeof(unsigned int));
```

> Username: pdimov  
> Created_at: 2017-11-08 22:29:07 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149816399  

This checks the iterator size, which is irrelevant. It should check  
  
- whether the `value_type` of the iterator is integral;  
- whether the `value_type` of the iterator is unsigned;  
- whether `numeric_limits<value_type>::max()` is >= 2^32-1, but this can only be tested at compile time on C++11, so  
- whether `sizeof(value_type) * CHAR_BIT >= 32`.

> Username: jeking3  
> Created_at: 2017-11-08 22:59:21 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149822806  

Okay, I will update this.

> Username: jeking3  
> Created_at: 2017-11-08 23:41:08 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149830420  

I tried this but it didn't work - I'll keep on it.  
```  
        typedef Iter::value_type iter_value_type;  
        BOOST_STATIC_ASSERT(is_integral<iter_value_type>::value == true_type);  
        BOOST_STATIC_ASSERT(is_unsigned<iter_value_type>::value == true_type);  
        BOOST_STATIC_ASSERT(sizeof(iter_value_type) * CHAR_BIT >= 32);  
```  
In reading [SeedSeq documentation](http://www.boost.org/doc/libs/1_65_1/doc/html/boost_random/reference.html#boost_random.reference.concepts.seed_sequence), Iter can be a class with a value_type type definition of an unsigned integral and an operator*, or it can be a pointer to an unsigned integral, correct?  I'm not certain (yet) how I would test for both.

> Username: pdimov  
> Created_at: 2017-11-08 23:48:24 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149831548  

`typedef std::iterator_traits<Iter>::value_type value_type;`

> Username: pdimov  
> Created_at: 2017-11-08 23:50:26 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149831867  

Then just `BOOST_STATIC_ASSERT( boost::is_integral<value_type>::value );`, comparing to the type `true_type` doesn't make sense.

> Username: pdimov  
> Created_at: 2017-11-08 23:51:52 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149832076  

It will work in C++11 because `true_type` has a `constexpr` conversion to `true` but is still nonsensical. :-)

> Username: jeking3  
> Created_at: 2017-11-09 00:16:13 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149835870  

I want to support C++03, will it still work?  
I don't think I have ever used std::iterator_traits before.  
I'm learning a ton of new tricks, thanks!


---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2017-11-08 22:31:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75273335  

📁 include/boost/uuid/detail/random_provider.hpp

```diff
  46 |+         {
  47 |+             get_random_bytes(&*first, sizeof(*first));
  48 |+             *first &= (std::numeric_limits<unsigned int>::max)();
```

> Username: pdimov  
> Created_at: 2017-11-08 22:31:05 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149816776  

Not correct. `generate` is required to produce numbers in [0..2^32-1], regardless of the size of `unsigned int`.

> Username: jeking3  
> Created_at: 2017-11-08 22:59:41 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149822862  

Got it, I will update this.


---

## Review 4 [Commented]

> Username: pdimov  
> Created_at: 2017-11-08 22:32:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75273705  

📁 include/boost/uuid/detail/random_provider.hpp

```diff
  19 |+ 
  20 |+ #include <boost/uuid/detail/random_provider_detect_platform.hpp>
  21 |+ #include BOOST_UUID_RANDOM_PROVIDER_BASE_INCLUDE()
```

> Username: pdimov  
> Created_at: 2017-11-08 22:32:40 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149817160  

It's better to just include the appropriate platform header from `random_provider_detect_platform.hpp`, renaming it into `random_provider_base.hpp` in the process, because the above confuses dependency scanners; they can't see through the macro.

> Username: jeking3  
> Created_at: 2017-11-08 23:01:08 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149823167  

I needed platform detection separate from loading the implementation to make the mocking work properly.  I can expand that out so the macro isn't there and put additional preprocessor branches based on the `BOOST_UUID_RANDOM_PROVIDER_*` definitions made by the detection logic in that header.

> Username: pdimov  
> Created_at: 2017-11-08 23:10:17 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149824844  

Yes, it's better to spell it out with #ifdefs instead of a macro include.


---

## Review 5 [Commented]

> Username: pdimov  
> Created_at: 2017-11-08 22:34:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75274202  

📁 doc/uuid.html

```diff
 643 |+             <li>
 644 |+                 <tt>BOOST_UUID_RANDOM_PROVIDER_FORCE_POSIX</tt>: on Unix this will force the
 645 |+                 selection of <tt>/dev/*random</tt> over getentropy(3).
```

> Username: pdimov  
> Created_at: 2017-11-08 22:34:39 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149817589  

Prefer to not provide this. By all means give people a macro to override behavior *when they ask for it*, no need to to it preemptively.

> Username: jeking3  
> Created_at: 2017-11-08 23:05:04 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149823858  

I'm leveraging this to get coverage and a (mostly) common sad path test.  I'll look at testing against the posix.ipp implementation directly when running on linux.

> Username: pdimov  
> Created_at: 2017-11-08 23:11:13 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149825047  

Yes, I hadn't yet gotten to the testing part. Keep those macros. I was looking at them from the user side.

> Username: jeking3  
> Created_at: 2017-11-08 23:16:28 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149826017  

That'll save a bunch of additional work... thanks.


---

## Review 6 [Commented]

> Username: pdimov  
> Created_at: 2017-11-08 22:35:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75274424  

📁 doc/uuid.html

```diff
 647 |+             <li>
 648 |+                 <tt>BOOST_UUID_RANDOM_PROVIDER_FORCE_WINCRYPT</tt>: on Windows this will force the
 649 |+                 selection of Wincrypt over BCrypt.
```

> Username: pdimov  
> Created_at: 2017-11-08 22:35:34 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149817779  

Ditto. No need to ever prefer the older API. If someone asks for it, fine; until then, no need.

> Username: jeking3  
> Created_at: 2017-11-08 23:06:36 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149824194  

I'm leveraging this to get coverage and a (mostly) common sad path test.  This test is more complex as I have to build a DLL and link against it, but I can make another test file that's specific to the wincrypt.ipp file and loads the mock library.


---

## Review 7 [Commented]

> Username: pdimov  
> Created_at: 2017-11-08 22:36:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75274769  

📁 doc/uuid.html

```diff
 654 |+             <li>
 655 |+                 <tt>BOOST_UUID_RANDOM_PROVIDER_POSIX_BLOCKING</tt>: when using <tt>/dev/*random</tt>,
 656 |+                 if this is defined, skip attempting to open <tt>/dev/urandom</tt> first.
```

> Username: pdimov  
> Created_at: 2017-11-08 22:36:56 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149818125  

Nope. Not only should `/dev/random` not be configurable to be used as a preference, it should never be used, anywhere, for anything, as the OpenBSD docs say.

> Username: jeking3  
> Created_at: 2017-11-08 23:07:14 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149824307  

Easy to remove.  Thanks.  Less options too and it simplifies the code and the sad test path that has to account for two open()s in the failure case.


---

## Review 8 [Commented]

> Username: pdimov  
> Created_at: 2017-11-08 22:39:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75275330  

📁 include/boost/uuid/detail/random_provider_bcrypt.ipp

```diff
  61 |+             boost::winapi::BCryptGenRandom(
  62 |+                 hProv_,
  63 |+                 reinterpret_cast<boost::winapi::PUCHAR_>(buf),
```

> Username: pdimov  
> Created_at: 2017-11-08 22:39:08 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149818579  

`static_cast` is better, when it works, which it does here.

> Username: jeking3  
> Created_at: 2017-11-08 23:07:50 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149824423  

Agree, I'll change it.  I usually try static_cast first and let the compiler force me the other way.


---

## Review 9 [Commented]

> Username: pdimov  
> Created_at: 2017-11-08 22:40:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75275550  

📁 include/boost/uuid/detail/random_provider_detect_platform.hpp

```diff
  14 |+ #include <boost/predef/library/c/gnu.h>
  15 |+ #include <boost/predef/platform/cloudabi.h>
  16 |+ // #include <boost/predef/os/bsd/open.h> re-enable when Boost.Predef #66 clears
```

> Username: pdimov  
> Created_at: 2017-11-08 22:40:07 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149818781  

No need to wait for Predef. The functions we need are available on OpenBSD 2.5 and above.

> Username: jeking3  
> Created_at: 2017-11-08 23:09:13 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149824664  

Good to know, I was planning on getentropy() there so... (on to the next comment)


---

## Review 10 [Commented]

> Username: pdimov  
> Created_at: 2017-11-08 22:42:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75276165  

📁 include/boost/uuid/detail/random_provider_detect_platform.hpp

```diff
  46 |+    && !defined(BOOST_UUID_RANDOM_PROVIDER_FORCE_POSIX)
  47 |+ /* when Boost.Prefef #6 merges, allow this based on OpenBSD documentation:
  48 |+ BOOST_OS_BSD_OPEN >= BOOST_VERSION_NUMBER(5, 6, 0)) */
```

> Username: pdimov  
> Created_at: 2017-11-08 22:42:38 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149819322  

"getentropy() is not intended for regular code; please use the arc4random(3) family of functions instead."  
  
https://man.openbsd.org/getentropy.2  
  
Heed what the OpenBSD people say. They know their stuff. :-)

> Username: jeking3  
> Created_at: 2017-11-08 23:09:22 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149824700  

and there you go.


---

## Review 11 [Commented]

> Username: pdimov  
> Created_at: 2017-11-08 22:46:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75277104  

📁 include/boost/uuid/detail/random_provider_wincrypt.ipp

```diff
  19 |+ #include <string>
  20 |+ 
  21 |+ #if !defined(BOOST_UUID_RANDOM_PROVIDER_NO_LIB)
```

> Username: pdimov  
> Created_at: 2017-11-08 22:46:39 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149820226  

BOOST_ALL_NO_LIB, as I already said.

> Username: jeking3  
> Created_at: 2017-11-08 23:12:21 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149825249  

Sorry I missed that!  Isn't each library capable of having its own preprocessor macros in this area, like `BOOST_SYSTEM_NO_LIB`, `BOOST_CONFIG_NO_LIB`, `BOOST_PYTHON_NO_LIB`...?

> Username: pdimov  
> Created_at: 2017-11-08 23:14:11 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149825601  

It is, but you have to check the global macro as well and not autolink if it's set. It's a global user switch that requests all autolinking to be disabled, anywhere.

> Username: jeking3  
> Created_at: 2017-11-09 00:46:19 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149840414  

In my builds on Windows with msvc-14.1, BOOST_ALL_NO_LIB is set to 1 by default.  This seems odd - why would it be set on the way in?  Boost.Uuid does not set it.

> Username: pdimov  
> Created_at: 2017-11-09 00:52:18 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149841257  

Interesting. No idea why it's set; you'll need to investigate.

> Username: jeking3  
> Created_at: 2017-11-09 00:53:56 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149841515  

It is defined for all builds of boost, it's in boost.jam.  I will add BOOST_UUID_SOURCE and make it conditional on that as well.  This pattern is found elsewhere.


---

## Review 12 [Commented]

> Username: pdimov  
> Created_at: 2017-11-08 22:47:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75277378  

📁 include/boost/uuid/detail/random_provider_wincrypt.ipp

```diff
  44 |+           boost::winapi::CryptAcquireContextW
  45 |+ #else
  46 |+           boost::winapi::CryptAcquireContextA
```

> Username: pdimov  
> Created_at: 2017-11-08 22:47:57 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149820481  

No reason to ever use the A function here, if you ask me.

> Username: jeking3  
> Created_at: 2017-11-08 23:14:44 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149825702  

I believe you may be right now that the provider string / enumeration stuff is gone.  If so I'll simplify and remove the additional test for this macro.


---

## Comment 13

> Username: pdimov  
> Created_at: 2017-11-08 22:57:25 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-342989587  

Ah. It now occurs to me that you need the configuration macros in order to force-test a provider when it wouldn't be selected otherwise. Makes sense.

---

## Comment 14

> Username: jeking3  
> Created_at: 2017-11-08 23:13:20 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-342993256  

That's what I was using them for, however I could write one test per provider implementation instead I suppose.  I just wanted it to be as uniform as possible since they all follow the same pattern.

---

## Comment 15

> Username: pdimov  
> Created_at: 2017-11-08 23:16:47 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-342993961  

Yes, you could include the implementation header directly and test that, which is still not a bad idea, but the other way is good too, as it can test the component integrated into the rest of the code. So, either way is fine with me, I suppose.

---

## Comment 16

> Username: jeking3  
> Created_at: 2017-11-08 23:18:14 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-342994232  

As an aside, we need to get this level of CI integration into every project.

---

## Comment 17

> Username: pdimov  
> Created_at: 2017-11-08 23:20:20 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-342994661  

It still seems to me that RtlGenRandom is quite a convenient choice on Windows as it requires no setup and is quite likely a bit faster than firing up a bcrypt/wincrypt provider. I'm not entirely clear on why you dislike it so. :-)

---

## Comment 18

> Username: jeking3  
> Created_at: 2017-11-08 23:35:03 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-342997696  

I believe it would still have a setup cost insofar as needing to perhaps statically initialize the address of the API call through LoadLibrary and GetProcAddress, and in a previous PR I was told to avoid using statics.  I agree those two calls are probably much lighter than wincrypt.  I can give it a try.

---

## Comment 19

> Username: pdimov  
> Created_at: 2017-11-08 23:37:22 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-342998114  

There's no need for `LoadLibrary`.  
  
```  
extern "C" unsigned char __stdcall SystemFunction036( void * p, unsigned long n );  
```  
  
works for me.

---

## Comment 20

> Username: jeking3  
> Created_at: 2017-11-08 23:42:40 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-342999116  

You are assuming that advapi32.dll has already been loaded, and I think that's an unsafe assumption to make.

---

## Comment 21

> Username: pdimov  
> Created_at: 2017-11-08 23:53:34 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343001138  

It works for me in a sample program. Not sure how I could be assuming anything if the program links.

---

## Comment 22

> Username: pdimov  
> Created_at: 2017-11-08 23:57:05 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343001832  

```  
extern "C" unsigned char __stdcall SystemFunction036( void * p, unsigned long n );  
  
bool RtlGenRandom( void * p, unsigned long n )  
{  
	return SystemFunction036( p, n );  
}  
  
#include <iostream>  
  
int main()  
{  
	unsigned x = 0;  
	bool r = RtlGenRandom( &x, sizeof(x) );  
  
	std::cout << x << std::endl;  
}  
```  
  
```  
C:\Projects\testbed>g++ testbed.cpp  
  
C:\Projects\testbed>a  
725953787  
  
C:\Projects\testbed>clang++ testbed.cpp  
  
C:\Projects\testbed>a  
1701069008  
```

---

## Comment 23

> Username: jeking3  
> Created_at: 2017-11-09 00:14:57 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343005228  

You are still relying on the fact that ADVAPI32.DLL was loaded.  While it is in the default library list for most build systems, that can be overridden.  ADVAPI32.DLL is not available on Windows CE.  Pretty much everyone will be using bcrypt at this point with wincrypt left to cover older platforms.  Since that code has worked for a very long time here and in Boost.Random, and seeing as to how it's out of the normal code path, doesn't it make sense to leave it as is?

---

## Comment 24

> Username: Lastique  
> Created_at: 2017-11-09 00:17:18 UTC  
> Updated_at: 2017-11-09 00:18:55 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343005684  

@pdimov The `LoadLibrary`/`GetProcAddress` approach is what MS suggests [here](https://msdn.microsoft.com/en-us/library/windows/desktop/aa387694(v=vs.85).aspx). All in all, this function seems like one of those semi-legal half-documented APIs that are Windows implementation details and can change at any time. IMHO, we can use such APIs but only if we have to. If there is an official public API that covers our case, we should preferably use it.

---

## Comment 25

> Username: jeking3  
> Created_at: 2017-11-09 00:24:23 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343006963  

Okay that actually wasn't too bad, I'm doing some testing...

---

## Comment 26

> Username: pdimov  
> Created_at: 2017-11-09 00:25:09 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343007102  

It's impossible for a library to not be loaded if you link to it. If that's unsafe, your code is at least 20 times as unsafe.

---

## Comment 27

> Username: jeking3  
> Created_at: 2017-11-09 00:25:12 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343007114  

Would you prefer that I push a delta rather than a full rebase - would that make the next round easier?

---

## Comment 28

> Username: pdimov  
> Created_at: 2017-11-09 00:27:28 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343007522  

> All in all, this function seems like one of those semi-legal half-documented APIs that are Windows implementation details and can change at any time.  
  
I'm willing to bet a considerable amount of money that it won't because approximately all Windows programs presently in existence link to it. And if it does, we'll fix our code. This is not rocket science.

---

## Comment 29

> Username: pdimov  
> Created_at: 2017-11-09 00:30:05 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343007976  

Full rebase will still be better, I think.

---

## Comment 30

> Username: pdimov  
> Created_at: 2017-11-09 00:48:30 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343011037  

On second thought, it probably doesn't matter. You could just as well push another commit here.  
  
Benchmark results including RtlGenRandom will be appreciated.

---

## Comment 31

> Username: Lastique  
> Created_at: 2017-11-09 00:53:33 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343011882  

On November 9, 2017 3:27:29 AM Peter Dimov <notifications@github.com> wrote:  
  
>> All in all, this function seems like one of those semi-legal   
>> half-documented APIs that are Windows implementation details and can change   
>> at any time.  
>  
> I'm willing to bet a considerable amount of money that it won't because   
> approximately all Windows programs presently in existence link to it. And   
> if it does, we'll fix our code. This is not rocket science.  
  
I won't argue, I myself am guilty in using some non-public APIs. :)

---

## Comment 32

> Username: jeking3  
> Created_at: 2017-11-09 00:58:32 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343012710  

I'm just not comfortable using RtlGenRandom in this context.  If this code moves over to Boost.Random in the future (that's where it really belongs anyway), if someone else wants to take on the potential burden of dealing with the consequences of that in order to squeeze a couple percent points of performance out of it, and in that context it probably makes good sense to be vigilant of things like that, then so be it.

---

## Comment 33

> Username: pdimov  
> Created_at: 2017-11-09 01:01:49 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343013299  

There are no consequences. It just works.

---

## Comment 34

> Username: swatanabe  
> Created_at: 2017-11-09 01:04:04 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343013691  

AMDG  
  
On 11/08/2017 05:46 PM, James E. King, III wrote:  
>   
> In my builds on Windows with msvc-14.1, BOOST_ALL_NO_LIB is set to 1 by default.  This seems odd - why would it be set on the way in?  Boost.Uuid does not set it.  
>   
  
  Boost.Build sets BOOST_ALL_NO_LIB globally, since the  
library dependencies are managed explicitly.  It's set  
in Jamroot and inherited by all Boost subprojects.  
  
In Christ,  
Steven Watanabe

---

## Comment 35

> Username: pdimov  
> Created_at: 2017-11-09 01:11:26 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343014974  

Hm. This means that `BOOST_*_NO_LIB` is not the proper mechanism to disable autolinking to non-Boost libraries.  
  
I remember user complaints that their `#define BOOST_ALL_NO_LIB` did not disable autolink to non-Boost libs, but it appears that it cannot be used in such a manner.  
  
The original UUID code just autolinked unconditionally, and I don't remember anyone complaining about it specifically.  
  
So maybe leave it as it was, `#ifndef BOOST_UUID_RANDOM_PROVIDER_NO_LIB`, although the people who'd have problem with autolinking to Windows libraries usually want a global off switch, not to hunt down tens of separate macros.

---

## Comment 36

> Username: pdimov  
> Created_at: 2017-11-09 01:12:58 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343015205  

> but it appears that it cannot be used in such a manner.  
  
Or perhaps not. When using Boost.Build, the Jamfile can just link to the proper library, as it already does now on gcc.

---

## Review 37 [Commented]

> Username: pdimov  
> Created_at: 2017-11-09 01:15:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75305049  

📁 include/boost/uuid/detail/random_provider.hpp

```diff
  58 |+         {
  59 |+             get_random_bytes(&*first, sizeof(*first));
  60 |+             *first &= (std::numeric_limits<boost::uint32_t>::max)();
```

> Username: pdimov  
> Created_at: 2017-11-09 01:15:52 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149844548  

`uint32_t` is not required to exist, and we already know its `max`: `0xFFFFFFFF`. So just use it.

> Username: jeking3  
> Created_at: 2017-11-09 01:18:34 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149844880  

`<boost/cstdint.hpp>` guarantees a proper definition.  Isn't `std::numeric_limits<T>::max` a compile-time constant?  It shouldn't matter.

> Username: pdimov  
> Created_at: 2017-11-09 01:34:43 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149846997  

uint32_t only exists when the platform has a 32 bit type.  
  
Why would anyone prefer to write `(std::numeric_limits<boost::uint32_t>::max)();` instead of `0xFFFFFFFF` is beyond me.

> Username: pdimov  
> Created_at: 2017-11-09 15:15:08 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149989599  

0xFFFFFFFF. :-)


---

## Review 38 [Commented]

> Username: pdimov  
> Created_at: 2017-11-09 01:19:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75305554  

📁 include/boost/uuid/detail/random_provider_bcrypt.ipp

```diff
  15 |+ #include <string>
  16 |+ 
  17 |+ #if defined(BOOST_UUID_SOURCE) || (!defined(BOOST_ALL_NO_LIB) && !defined(BOOST_UUID_RANDOM_PROVIDER_NO_LIB))
```

> Username: pdimov  
> Created_at: 2017-11-09 01:19:16 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149844979  

`BOOST_UUID_SOURCE` is by convention set when the source of the library is compiled - which we don't have as we're header-only. So it's not quite the right macro name to use here.

> Username: jeking3  
> Created_at: 2017-11-09 01:20:42 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149845162  

Okay I will try to switch to adding the lib in the jamfile.

> Username: jeking3  
> Created_at: 2017-11-09 01:35:27 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149847092  

I renamed the definition to `BOOST_UUID_FORCE_AUTO_LINK` and kept the same logic.  It does not have to be put into the documentation.

> Username: pdimov  
> Created_at: 2017-11-09 01:41:51 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149847889  

That's probably easiest, otherwise the selection logic would have to be replicated in the Jamfile which would probably be a challenge. :-)

> Username: pdimov  
> Created_at: 2017-11-09 01:43:36 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149848106  

We could link to `advapi32` regardless as everyone links to it anyway, WinCE probably not a concern because I suspect not many use Boost.Build there, but I'm not sure how to decide whether to link to `bcrypt`.

> Username: jeking3  
> Created_at: 2017-11-09 01:56:38 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149849663  

These auto-link statements are designed to make it easier for folks consuming Boost.Uuid regardless of the build system.


---

## Review 39 [Commented]

> Username: pdimov  
> Created_at: 2017-11-09 01:21:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75305810  

📁 include/boost/uuid/detail/random_provider_posix.ipp

```diff
  75 |+         {
  76 |+             ssize_t sz = BOOST_UUID_RANDOM_PROVIDER_POSIX_IMPL_READ(
  77 |+                 fd_, reinterpret_cast<char *>(buf) + offset, siz - offset);
```

> Username: pdimov  
> Created_at: 2017-11-09 01:21:08 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149845212  

static_cast :-)

> Username: jeking3  
> Created_at: 2017-11-09 01:21:48 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149845289  

I got one of them before...


---

## Comment 40

> Username: jeking3  
> Created_at: 2017-11-09 01:21:19 UTC  
> Updated_at: 2017-11-09 01:21:28 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343016681  

Do we have any OpenBSD test rigs in the matrix that will verify the happy path for arc4random?

---

## Review 41 [Commented]

> Username: pdimov  
> Created_at: 2017-11-09 01:21:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75305905  

📁 include/boost/uuid/detail/random_provider_wincrypt.ipp

```diff
  23 |+ #      define BOOST_LIB_NAME "coredll"
  24 |+ #   else
  25 |+ #      define BOOST_LIB_NAME "advapi32"
```

> Username: pdimov  
> Created_at: 2017-11-09 01:21:46 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149845287  

:-)


---

## Review 42 [Commented]

> Username: pdimov  
> Created_at: 2017-11-09 01:22:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75305990  

📁 include/boost/uuid/detail/random_provider_wincrypt.ipp

```diff
  66 |+     {
  67 |+         if (!boost::winapi::CryptGenRandom(hProv_, siz, 
  68 |+                     reinterpret_cast<boost::winapi::BYTE_ *>(buf)))
```

> Username: pdimov  
> Created_at: 2017-11-09 01:22:27 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149845348  

static_cast :-)


---

## Review 43 [Commented]

> Username: pdimov  
> Created_at: 2017-11-09 01:24:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75306235  

📁 include/boost/uuid/random_generator.hpp

```diff
  40 |-             ( (std::numeric_limits<unsigned long>::min)()
  41 |-             , (std::numeric_limits<unsigned long>::max)()
  66 |+             ( (std::numeric_limits<unsigned int>::min)()
```

> Username: pdimov  
> Created_at: 2017-11-09 01:24:13 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149845571  

Not clear why these changes from ulong to uint are needed. Would probably be better to keep the original to cut down on the delta unless there's a good reason to switch.


---

## Review 44 [Commented]

> Username: pdimov  
> Created_at: 2017-11-09 01:29:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75306950  

📁 test/Jamfile.v2

```diff
  12 |+ 
  13 |+       # BOOST_AUTO_LINK does not work outside of MSVC on Windows so this makes cygwin, mingw-w64 work:
  14 |+       <toolset>gcc,<target-os>windows:<linkflags>-lbcrypt
```

> Username: pdimov  
> Created_at: 2017-11-09 01:29:15 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149846211  

We could link here regardless of whether the toolset is gcc (there's also clang btw), and this will fix the lack of autolink when using Boost.Build. But the problem is that we don't necessarily know what to link to.  
  
`<linkflags>-lbcrypt` isn't correct though, I think that we need `lib bcrypt ;` and then `<library>bcrypt` here.

> Username: jeking3  
> Created_at: 2017-11-09 01:37:36 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149847364  

I will try that and see what it does to the mocks.

> Username: jeking3  
> Created_at: 2017-11-09 01:50:00 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149848914  

Is there any way to negate this (`<target-os>windows:<library>bcrypt`), so that I can set it in project requirements so everything links against it by default, but then in a specific mock based test negate it so it does NOT link against bcrypt?

> Username: pdimov  
> Created_at: 2017-11-09 01:58:16 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149849876  

I vaguely recall that one could use `<x>y:-<library>bcrypt` to remove it, but I won't be surprised if it doesn't work and I'm imagining things. :-)

> Username: pdimov  
> Created_at: 2017-11-09 02:00:38 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149850141  

Turns out I'm not, in this case. http://www.boost.org/build/doc/html/bbv2/overview/targets.html#bbv2.overview.targets.requirements

> Username: jeking3  
> Created_at: 2017-11-09 02:01:47 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149850269  

A change as proposed would break, for example, MinGW (32-bit) where bcrypt is not part of the distribution, so the bjam directives would need to be as smart as the preprocessor selection logic.  In fact I bet mingw is already broken, but it hasn't been updated since 2013 so I'm not going to worry about it.  I think I'll keep the auto-link and global gcc/windows directives as-is as they work in CI (well, I'll change it from linkflags to library).

> Username: pdimov  
> Created_at: 2017-11-09 02:09:24 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149851184  

Not sure what change would break what, as you're already linking to bcrypt in the gcc case.

> Username: pdimov  
> Created_at: 2017-11-09 02:12:57 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149851626  

> In fact I bet mingw is already broken  
  
There are many mingws, so some could be. I tried the one I have here, and it passes the tests except for test_serialization.exe, which crashes. But that may well be Serialization's fault.

> Username: pdimov  
> Created_at: 2017-11-09 02:15:57 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149851984  

Appveyor has the plain mingw in C:\mingw, so you could test it in addition to mingw-w64, which you currently have. See f.ex. https://ci.appveyor.com/project/pdimov/system

> Username: jeking3  
> Created_at: 2017-11-09 02:16:33 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149852047  

Take a look at my appveyor config; I found that a little while ago and marked some jobs as allowed to fail.  Serialization has a large bug backlog (40 to 50) in trac... I don't know if I reported it.

> Username: pdimov  
> Created_at: 2017-11-09 02:21:14 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149852636  

Odd, test_serialization passed the second time. Mystery.

> Username: pdimov  
> Created_at: 2017-11-09 02:24:50 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149853032  

Instead of not testing anything, it might be better to disable just test_serialization on mingw and keep testing the rest.

> Username: pdimov  
> Created_at: 2017-11-09 02:27:55 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149853378  

Cygwin, on the other hand, passes with flying colors.

> Username: jeking3  
> Created_at: 2017-11-09 02:29:09 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149853528  

I am unable to use the syntax: `<target-os>windows:-<library>bcrypt`, or `<target-os>windows:-<define>BOOST_ALL_NO_LIB` - both fail in bjam as an invalid target-os.  I'm going back to the logic I had.

> Username: pdimov  
> Created_at: 2017-11-09 02:33:49 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149854065  

If only there existed a simple function in `advapi32` that we could just call and avoid all that hassle.


---

## Review 45 [Commented]

> Username: pdimov  
> Created_at: 2017-11-09 01:30:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75307134  

📁 test/Jamfile.v2

```diff
 162 |+       : : :
 163 |+         <define>BOOST_UUID_RANDOM_PROVIDER_FORCE_POSIX                  # will force POSIX over getentropy
 164 |+         <define>BOOST_UUID_RANDOM_PROVIDER_POSIX_BLOCKING               # will force /dev/random over /dev/urandom
```

> Username: pdimov  
> Created_at: 2017-11-09 01:30:28 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149846388  

No longer necessary.


---

## Review 46 [Commented]

> Username: pdimov  
> Created_at: 2017-11-09 01:32:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75307389  

📁 test/test_entropy_error.cpp

```diff
  19 |+     entropy_error err(6, "foo");
  20 |+     BOOST_TEST_EQ(6, err.errcode());
  21 |+     BOOST_TEST_EQ(std::string("foo"), std::string(err.what()));
```

> Username: pdimov  
> Created_at: 2017-11-09 01:32:15 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149846630  

Could use BOOST_TEST_CSTR_EQ here.


---

## Comment 47

> Username: swatanabe  
> Created_at: 2017-11-09 02:35:10 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343029242  

AMDG  
  
On 11/08/2017 07:29 PM, James E. King, III wrote:  
> jeking3 commented on this pull request.  
>   
>   
>   
>> @@ -7,10 +7,18 @@  
>  project libs/uuid/test  
>      : requirements  
>    
> -      # boost::random requires this setting for a warning free build:  
> +	  # define BOOST_UUID_SOURCE to defeat the default BOOST_ALL_NO_LIB in boost.jam  
> +	  <define>BOOST_UUID_SOURCE=1  
> +  
> +      # BOOST_AUTO_LINK does not work outside of MSVC on Windows so this makes cygwin, mingw-w64 work:  
> +      <toolset>gcc,<target-os>windows:<linkflags>-lbcrypt   
>   
> I am unable to use the syntax: `<target-os>windows:-<library>bcrypt`, or `<target-os>windows:-<define>BOOST_ALL_NO_LIB` - both fail in bjam as an invalid target-os.  I'm going back to the logic I had.  
>   
  
  The '-property' syntax is not very user-friendly.  Basically,  
the property that you remove must be exactly the same as the  
property that was added:  
-<toolset>gcc,<target-os>windows:<linkflags>-lbcrypt  
  
In Christ,  
Steven Watanabe

---

## Comment 48

> Username: jeking3  
> Created_at: 2017-11-09 14:20:38 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343168283  

I have some changes to support building for CloudABI compatibility since it's in this pull request, now that I was able to build the Boost.Uuid tests under CloudABI.  These depend on https://github.com/boostorg/predef/pull/68 so I won't push them until that merges.

---

## Comment 49

> Username: jeking3  
> Created_at: 2017-11-09 14:58:55 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343180068  

One of the developers from CloudABI pointed me to arc4random in cloudlibc so I'm going to remove the cloudabi specific implementation and have it use arc4random.

---

## Comment 50

> Username: jeking3  
> Created_at: 2017-11-09 15:00:42 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343180683  

I'm curious what you think about the scope of this change relative to the upcoming release.  I have it marked as a milestone for 1.67.0 and not 1.66.0 right now because it is a lot of change, however it's well tested on every platform, both happy and sad paths.

---

## Comment 51

> Username: pdimov  
> Created_at: 2017-11-09 15:02:03 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343181133  

1.66.0 is closed for beta now so I'd say we missed that boat.

---

## Review 52 [Commented]

> Username: pdimov  
> Created_at: 2017-11-09 15:14:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75470860  

📁 appveyor.yml

```diff
  54 |+       ADDPATH: C:\mingw\bin;
  55 |+       TOOLSET: gcc
  56 |+       CXXFLAGS: cxxflags=-std=c++03
```

> Username: pdimov  
> Created_at: 2017-11-09 15:14:23 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149989371  

The new way is `cxxstd=03` instead of using `cxxflags`, which allows testing `cxxstd=03,11` or even `cxxstd=03,11,14,1z` in one go instead of using multiple jobs (slow on Appveyor as they're serialized).

> Username: jeking3  
> Created_at: 2017-11-09 16:53:13 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r150021410  

Sorry, I just re-enabled an old stanza here.  The other ones should be using that.  If not, I'll adjust.

> Username: pdimov  
> Created_at: 2017-11-09 16:58:53 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r150023245  

All of the jobs at https://ci.appveyor.com/project/jeking3/uuid-gaamf/build/1.0.73-develop seem to be using `cxxflags`.

> Username: jeking3  
> Created_at: 2017-11-09 17:54:20 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r150038220  

I was thinking about Travis CI, I'll get this fixed; it's queued up behind https://github.com/boostorg/predef/pull/68 right now.


---

## Review 53 [Commented]

> Username: pdimov  
> Created_at: 2017-11-09 15:17:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/53#pullrequestreview-75471823  

📁 include/boost/uuid/detail/random_provider_wincrypt.ipp

```diff
  26 |+ #   endif
  27 |+ #   define BOOST_AUTO_LINK_NOMANGLE
  28 |+ #   include <boost/config/auto_link.hpp>
```

> Username: pdimov  
> Created_at: 2017-11-09 15:17:01 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149990234  

I've a strong suspicion that these autolinks are unnecessary. Can't imagine "coredll" not being linked on CE, and "advapi32" is on desktop.

> Username: pdimov  
> Created_at: 2017-11-09 15:22:14 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149991757  

Hm, nope, a plain `cl` doesn't link `advapi32` by default, so we do need it, unless Winapi autolinks it for us.

> Username: pdimov  
> Created_at: 2017-11-09 15:25:00 UTC  
> Updated_at: 2017-11-10 12:32:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#discussion_r149992675  

Interestingly, Cygwin/Mingw g++/clang++ do autolink advapi32 though.


---

## Comment 54

> Username: pdimov  
> Created_at: 2017-11-09 15:29:05 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343189895  

This looks good. I however still think that it's way more complex than it would have been with RtlGenRandom on desktop. It's even available by default on Cygwin/Mingw, so things automatically work there even though autolink's not available.

---

## Comment 55

> Username: jeking3  
> Created_at: 2017-11-10 12:34:29 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343462293  

This (hopyfully last) refresh adds changes to support actually running the test suite on cloudabi in a FreeBSD VM.  I switched CloudABI to using arc4random as well.

---

## Comment 56

> Username: jeking3  
> Created_at: 2017-11-11 03:57:54 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-343638533  

@swatanabe with one more turn of the crank I think this would be something useful to consider moving into Boost.Random as a replacement for the existing random_device implementation.  Given that the random_device inside Boost.Random today is the only piece of code referencing Boost.System, if we did this then Boost.Random would no longer depend on Boost.System.  I don't know if that would free up Boost.Random from all library based dependencies though.  
  
A fairly simple random_device implementation based on this would be (incomplete...):  
  
```  
jking@ubuntu:~/boost/libs/uuid/test$ cat ../include/boost/uuid/detail/random_device.hpp   
#include <boost/uuid/detail/random_provider.hpp>  
  
namespace boost {  
namespace uuids {  
namespace detail {  
  
template<class Entropy>  
class random_device : public random_provider  
{  
public:  
    Entropy operator()()  
    {  
        Entropy e;  
        get_random_bytes(&e, sizeof(Entropy));  
        return e;  
    }  
};  
}}}  
  
  
jking@ubuntu:~/boost/libs/uuid/test$ git diff HEAD test_random_generator.cpp  
diff --git a/test/test_random_generator.cpp b/test/test_random_generator.cpp  
index bd9566d..6d4e4a0 100644  
--- a/test/test_random_generator.cpp  
+++ b/test/test_random_generator.cpp  
@@ -13,6 +13,7 @@  
 #include <boost/detail/lightweight_test.hpp>  
 #include <boost/predef/library/c/cloudabi.h>  
 #include <boost/random.hpp>  
+#include <boost/uuid/detail/random_device.hpp>  
 #include <boost/uuid/entropy_error.hpp>  
 #include <boost/uuid/random_generator.hpp>  
 #include <boost/uuid/uuid_io.hpp>  
@@ -117,5 +118,10 @@ int main(int, char*[])  
     }  
 #endif  
   
+    boost::uuids::detail::random_device<unsigned int> randgen;  
+    unsigned int i1 = randgen();  
+    unsigned int i2 = randgen();  
+    BOOST_TEST_NE(i1, i2);  
+  
     return boost::report_errors();  
 }  
```  
  
This would improve the platform coverage of random_device to include UWP and CloudABI, and use more optimal calls on newer versions of Linux.  The implementation is also fully tested (happy and sad paths), although the OpenBSD and CloudABI paths are tested manually unless I turn the build into a docker based build.  (I have extensive work doing in the Apache Thrift project, so I know this would be possible).

---

## Comment 57

> Username: jeking3  
> Created_at: 2017-11-15 17:22:39 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-344665105  

Should I complete the implementation as a random_device replacement and submit it to Boost.Random?

---

## Comment 58

> Username: jeking3  
> Created_at: 2017-12-05 15:38:13 UTC  
> Updated_at: 2017-12-05 15:41:48 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-349342060  

Now that 1.66.0 is closing out, I want to revisit this, but I need an answer, so please reply:  
  
**Should I complete the implementation as a random_device replacement and submit it to Boost.Random instead?**  
  
The benefits are:  
  
* Broader platform support for entropy generation  
* More optimal entropy generation leveraging the latest APIs  
* Lower maintenance cost  
* Reduced library dependencies (by decoupling random from system)  
* Comprehensive happy and sad path testing with every build (to the extent current CI allows)  
  
Thanks - Jim

---

## Comment 59

> Username: Lastique  
> Created_at: 2017-12-05 15:55:09 UTC  
> Updated_at: 2017-12-05 15:55:23 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-349347915  

I thought we've been over this and Boost.Random submission failed, haven't we?  
  
My opinion has not changed. You can do it however you like in implementation details of Boost.UUID. A fully compliant implementation of `random_device` in Boost.UUID details seem like an overkill to me; the functionality needed for Boost.UUID can be implemented much more simply. Depending on Boost.Random is only ok if it doesn't require linking with it (which basically means using `random_device` from Boost.Random is not an option).

---

## Comment 60

> Username: pdimov  
> Created_at: 2017-12-05 17:04:30 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-349370962  

The right place to ask this question is the list.

---

## Comment 61

> Username: jeking3  
> Created_at: 2017-12-07 23:33:52 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-350127591  

I submitted a PR into Boost.Random: https://github.com/boostorg/random/pull/34 - I hadn't heard back from Steven and didn't want to drag the issue up on the mailing list again.

---

## Comment 62

> Username: jgzhuang  
> Created_at: 2018-06-02 01:55:29 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-394048484  

@jeking3 Would you mind sharing the details of the "flaws" you mentioned in "Replaced the seed_rng detail implementation (which had a number of flaws) with a replacement header-only random_provider implementation."?   
I did notice a few strange things in the seed_rng in 1.66 and earlier (such as using the uninitialized content, new unsigned int and use the address and delete that unsigned int).  But I am interested in the flaws you discovered.   
  
Another implementation difference between seed_rng in 1.66 and the random_provider_base in 1.67 is that seed_rng uses std::fread while the random_provider_base uses ::read. With a synthetic testing it seems the latter is slower when siz is 4 although this only affects seeding.

---

## Comment 63

> Username: jeking3  
> Created_at: 2018-06-02 23:00:07 UTC  
> Url: https://github.com/boostorg/uuid/pull/53#issuecomment-394122898  

In particular the code was:  
 -  relying on uninitialized variables  
 -   did not check for errors while obtaining entropy  
 - had become difficult to maintain because the entropy generation was not well separated per platform.    
 - was doing way too much to try and be random, reading in process ID, thread ID, time of day, etc. and then running it through a sha1 hash, then pulling some bits.  Much better to use the operating system's entropy generator which was the primary purpose of the change, and to add more platform support.  
  
If you have evidence that shows ::read is slower than std::fread then I suggest opening an issue or a pull request with the evidence and the fix.  
  
Thanks!

---
