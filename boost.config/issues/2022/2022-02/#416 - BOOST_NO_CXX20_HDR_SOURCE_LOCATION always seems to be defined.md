# #416 - BOOST_NO_CXX20_HDR_SOURCE_LOCATION always seems to be defined [Closed]

> Username: pdimov  
> Created at: 2022-02-03 05:23:53 UTC  
> Updated at: 2022-02-03 18:55:35 UTC  
> Closed at: 2022-02-03 14:41:21 UTC  
> Url: https://github.com/boostorg/config/issues/416  

BOOST_NO_CXX20_HDR_SOURCE_LOCATION is defined on msvc latest -std:c++20: https://godbolt.org/z/557Gq4oce  
It's also defined on GCC 11 -std=c++20: https://godbolt.org/z/47c4v83dd  
  
Yet, `<source_location>` is present and fully working on both.  
  
I can see one possible problem at https://github.com/boostorg/config/blob/faa3bda19bcc3f0ac964bc98bc3121ed2455e8ed/include/boost/config/detail/suffix.hpp#L1162, where the L is uppercase, but I don't think that's the cause of either of the above.  
  
(For completeness, `<source_location>` also works on the latest MSVC 19.29 (https://godbolt.org/z/Gano8358T) and on MSVC 19.30 (https://godbolt.org/z/EMM7s5ccr)).

---

## Comment 1

> Username: pdimov  
> Created at: 2022-02-03 06:45:30 UTC  
> Url: https://github.com/boostorg/config/issues/416#issuecomment-1028650942  

Amusingly, BOOST_NO_CXX20_HDR_SOURCE_LOCATION is _not_ defined under clang-cl cxxstd=latest, but `<source_location>` doesn't work.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2022-02-03 11:56:00 UTC  
> Url: https://github.com/boostorg/config/issues/416#issuecomment-1028916374  

I'll fix the typo, but for me with _MSC_FULL_VER=193030706 I do not see that macro defined in C++20 mode.  
  
The only C+20 macros defined are BOOST_NO_CXX20_HDR_FORMAT and BOOST_NO_CXX20_HDR_RANGES.  
  
I do see it defined for msvc14.2: I'll look into that, and I suspect the typo is causing the gcc issues.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2022-02-03 12:57:16 UTC  
> Url: https://github.com/boostorg/config/issues/416#issuecomment-1028963966  

The referenced PR should address these issues, do you know if there's a way to test clang-cl under GHA CI?

---

## Comment 4

> Username: pdimov  
> Created at: 2022-02-03 14:32:35 UTC  
> Url: https://github.com/boostorg/config/issues/416#issuecomment-1029049517  

> I'll fix the typo, but for me with _MSC_FULL_VER=193030706 I do not see that macro defined in C++20 mode.  
  
Interesting, it's not getting defined for me locally either, but it is on CE. There must be something specific to the configuration there, because I checked the macros in the condition in dinkumware.hpp and they have the expected values.  
  
I couldn't get clang-cl working under GHA, so I rely on Appveyor (and local testing) for it.

---

## Comment 5

> Username: pdimov  
> Created at: 2022-02-03 14:56:40 UTC  
> Url: https://github.com/boostorg/config/issues/416#issuecomment-1029073496  

This has fixed GCC, but I honestly have no idea why. I don't see the macro being defined anywhere in libstdcpp3.hpp, and yet when I check locally with Cygwin GCC 11, it gets defined properly for -std=c++17 and doesn't get defined for -std=c++20. How does that happen?

---

## Comment 6

> Username: pdimov  
> Created at: 2022-02-03 15:14:23 UTC  
> Url: https://github.com/boostorg/config/issues/416#issuecomment-1029091985  

Oh I see, the generic section in suffix.hpp takes care of it, `< 201704` means C++17 because that's 201703L.  
  
The library feature macros (supposing `<version>` is available) seem pretty reliable under all three stdlibs, so I wonder why we're using these only for libc++ and not generically in suffix.

---

## Comment 7

> Username: jzmaddock  
> Created at: 2022-02-03 17:00:47 UTC  
> Url: https://github.com/boostorg/config/issues/416#issuecomment-1029199794  

>The library feature macros (supposing <version> is available) seem pretty reliable under all three stdlibs, so I wonder why we're using these only for libc++ and not generically in suffix.  
  
The starting point is to handle everything as generically as possible in suffix.hpp and just handle the special cases elsewhere, if you spot something that needs tidying up by all means submit a PR, but generally I try not to touch anything here that seems to be working, though I admit that tends to result in a certain amount of "historical" code ;)

---

## Comment 8

> Username: pdimov  
> Created at: 2022-02-03 17:06:26 UTC  
> Updated at: 2022-02-03 17:06:41 UTC  
> Url: https://github.com/boostorg/config/issues/416#issuecomment-1029205383  

Right, my suggestion was to handle everything post-`<version>` generically in `suffix.hpp` by checking the feature macros, instead of `__has_include`.  
  
(Prompted by the observation that checking `__cpp_lib_source_location` produces the correct result everywhere.)  
  
(This will allow us for instance to delete that same block from `libcpp.hpp`.)

---

## Comment 9

> Username: jzmaddock  
> Created at: 2022-02-03 17:24:55 UTC  
> Url: https://github.com/boostorg/config/issues/416#issuecomment-1029223514  

Nod.  I didn't realise quite how much duplication there was until after I posted :(  
  
However, I tried merging the libc++ logic into suffix.hpp and it certainly broke msvc config quite badly for reasons I don't understand yet.  It can handle the libc++ case though I suspect.

---

## Comment 10

> Username: jzmaddock  
> Created at: 2022-02-03 17:27:18 UTC  
> Url: https://github.com/boostorg/config/issues/416#issuecomment-1029225715  

My bad, I'd messed up the project config.... trying again...

---

## Comment 11

> Username: jzmaddock  
> Created at: 2022-02-03 18:55:35 UTC  
> Url: https://github.com/boostorg/config/issues/416#issuecomment-1029300058  

Should be addressed in https://github.com/boostorg/config/pull/421.
