# #193 Documentation update [Merged]

> Username: jzmaddock  
> Created at: 2020-02-07 18:22:51 UTC  
> Updated at: 2021-01-30 16:10:04 UTC  
> Merged at: 2020-03-19 16:55:10 UTC  
> Closed at: 2020-03-19 16:55:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193  

Made a PR so we can discuss the changes more easily.

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2020-02-07 18:27:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/193#pullrequestreview-355349695  

📁 config/Jamfile.v2

```diff
  42 |    <toolset>gcc:<cxxflags>-Wall
  43 |    <toolset>gcc:<cxxflags>-Wextra
  44 |+    <toolset>gcc:<cxxflags>-fext-numeric-literals  # enable suffix Q for float128.
```

> Username: jzmaddock  
> Created_at: 2020-02-07 18:27:50 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r376539168  

No, we *DO NOT* support -fext-numeric-literals only -std=gnu++XX - this puts gcc into an odd state where neither ourselves nor the C++ std lib can determine whether `__float128` is fully supported or not.  Plus the whole point of the configuration checks is to determine whether or not __float128 is fully supported -  we shouldn't be second guessing this, just determine whether the current compilation mode is OK or not.

> Username: pabristow  
> Created_at: 2020-02-11 16:02:47 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r377731443  

OK - My recollection is that the compiler suggested adding -fext-numeric-literals! (but that was using CodeBlocks IDE?)  
  
But using b2/bjam you are right that once cxxstd=17 (say) cxxstd-dialect=gnu is chosen, then _float128 is supported.  
  
So I have changed to this (in case anyone else should be deluded into thinking it is needed.  
`  
   #<toolset>gcc:<cxxflags>-fext-numeric-literals  # Do not need (nor should) enable suffix Q for float128.  
`


---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2020-02-07 18:30:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/193#pullrequestreview-355351385  

📁 config/has_constexpr_limits_cmd.cpp

```diff
  11 | #if __GNUC__ < 9
  10 |- #error "Older GCC versions don't support -fconstexpr-ops-limit"
  12 |+ #  error "Older GCC versions don't support -fconstexpr-ops-limit (aka -fconstexpr-loop-limit=n) ."
```

> Username: jzmaddock  
> Created_at: 2020-02-07 18:30:44 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r376540538  

I don't think these are the same at all?  -fconstexpr-loop-limit deals with loops within constexpr functions, where as -fconstexpr-ops-limits sets the total number of integer operations permitted.


---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2020-02-07 18:31:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/193#pullrequestreview-355351988  

📁 config/has_float128.cpp

```diff
  10 | #ifndef BOOST_HAS_FLOAT128
   9 |- #error "This doesn't work unless Boost.Config enables __float128 support"
  11 |+ #  error "This doesn't work unless Boost.Config enables __float128 support. Requires GCC using GNU C++compiler and option -fext-numeric-literals. For example: b2/bjam command "b2 float128_snips toolset=gcc-8.1.0 cxxstd=17 cxxstd-dialect=gnu address-model=64 release "
```

> Username: jzmaddock  
> Created_at: 2020-02-07 18:31:43 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r376541025  

As above, -fext-numeric-literals is not supported, only -std=gnu++XX

> Username: pabristow  
> Created_at: 2020-02-11 16:06:57 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r377734089  

Changed to `#  error "Older GCC versions don't support -fconstexpr-ops-limit (see also -fconstexpr-loop-limit=n) ."  
`


---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2020-02-07 18:38:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/193#pullrequestreview-355355730  

📁 config/has_is_constant_evaluated.cpp

```diff
   4 | //  LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)
   5 | 
   6 |+ // This program determines if std::is_constant_evaluated  is available to switch within a function to use compile-time constexp calculations.
```

> Username: jzmaddock  
> Created_at: 2020-02-07 18:38:10 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r376543891  

I'm not sure about these changes - the purpose of this configuration test is to determine whether BOOST_MP_HAS_IS_CONSTANT_EVALUATED has been set or not.  
  
* There are some compilers (gcc-6/7/8) which do support constexpr detection through means other than `std::is_constant_evaluated`, and in compilation modes other than C++2a.  
* Likewise `__builtin_is_constant_evaluated` is available in C++14 and later once it's been added, so some of these checks just create unnecessary warnings?  
* There are some compilers (Intel - support for which I'm still working on), which does support `__builtin_is_constant_evaluated` but which is too buggy to use it's C++14 constexpr support at all.  So again IMO this just generates unnecessary warning noise?


---

## Review 5 [Commented]

> Username: jzmaddock  
> Created_at: 2020-02-07 18:41:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/193#pullrequestreview-355357557  

📁 doc/html/standalone_HTML.manifest

```diff
   1 |+ index.html
```

> Username: jzmaddock  
> Created_at: 2020-02-07 18:41:26 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r376545371  

Did you mean to commit this one - these manifests are of no use to anyone that I can see?  
  
Not sure why they're generated at all to be honest... it's a docbook thing :(

> Username: pabristow  
> Created_at: 2020-02-11 16:54:07 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r377764788  

I just committed all the Quickbook generated files.  I can't see any particular use for the manifest.  
  
I've Perhaps this should be added to the .gitignore?  
  
I would also favour not committing the entire generated html and adding the /html folder to the 'gitignore.  I can see that it is convenient for other developers (or even users) who don't have to rebuild the docs.  But it is a PITA to have all these files that keep changing (and must be cluttering up GIThub).  Would generating a PDF version for 'users' be more convenient?  (It has the side effect of being more easily searchable: unless I know the right section, I haven't found how to search the html folder easily.  Once 'published' Google is our friend.


---

## Review 6 [Commented]

> Username: jzmaddock  
> Created_at: 2020-02-07 18:42:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/193#pullrequestreview-355358333  

📁 example/Jamfile.v2

```diff
  63 |-     ;
  63 |+       <toolset>clang-win:<link>static  # Clang-win does not generate .dlls.
  64 |+       <toolset>clang:<link>static # Clang-linux does not generate .dlls.
```

> Username: jzmaddock  
> Created_at: 2020-02-07 18:42:46 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r376545992  

Caution: this is only on windows, not Linux.


---

## Review 7 [Commented]

> Username: jzmaddock  
> Created_at: 2020-02-07 18:47:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/193#pullrequestreview-355361252  

📁 example/big_seventh.cpp

```diff
 149 |+ */
 150 |+   const cpp_bin_float_50 f2("1.234");  // Construct from decimal digit string.
 151 |+   std::cout << "cpp_bin_float_50 f2(\"1.234\") = " << f2 << std::endl; // 12.340000000000000000000000000000000000000000000000
```

> Username: jzmaddock  
> Created_at: 2020-02-07 18:47:38 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r376548295  

Oops, factor of 10 out?

> Username: pabristow  
> Created_at: 2020-02-11 16:56:10 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r377766088  

Oops indeed!


---

## Review 8 [Commented]

> Username: jzmaddock  
> Created_at: 2020-02-07 18:51:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/193#pullrequestreview-355363396  

📁 example/float128_snips.cpp

```diff
  42 |    /*->*/
  35 |-    constexpr float128 pi = 3.1415926535897932384626433832795028841971693993751058Q;
  43 |+    constexpr float128 pi = 3.14159265358979323846264338327950Q;
```

> Username: jzmaddock  
> Created_at: 2020-02-07 18:51:11 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r376549942  

That doesn't seem to have enough digits anymore?

> Username: pabristow  
> Created_at: 2020-02-14 16:49:49 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r379536451  

I reduced this to 36 decimal digits to match the number with the implied precision of float128 of std::numeric_limits::<float128>max_digits10 = 36 as I felt this was clearer.  But I could revert it if you don't think that this helps.

> Username: jzmaddock  
> Created_at: 2020-02-15 10:55:06 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r379824517  

There aren't 36 digits?

> Username: pabristow  
> Created_at: 2020-02-15 14:59:13 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r379836163  

You are, as always, this is digits10 rather than max_digits10.  I'll change it.


---

## Review 9 [Commented]

> Username: jzmaddock  
> Created_at: 2020-02-07 19:19:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/193#pullrequestreview-355380305  

📁 example/gauss_laguerre_quadrature.cpp

```diff
 227 |         const std::pair<T, T>
 228 |-           first_laguerre_root = boost::math::tools::bisect(laguerre_object,
 228 |+           first_laguerre_roots = boost::math::tools::bisect(laguerre_object,
```

> Username: jzmaddock  
> Created_at: 2020-02-07 19:19:58 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r376562768  

@NAThompson : `first_laguerre_root`(s) appears to be unused?  
  
There also seem to be some branches in the logic which do not set `first_laguerre_root_has_been_found`?

> Username: NAThompson  
> Created_at: 2020-02-07 23:05:32 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r376648633  

I'm baffled! I'm pretty sure I didn't write any of this, but there's my name in the copyright! I read the `git blame`; looks like @ckormanyos write it.

> Username: pabristow  
> Created_at: 2020-02-11 17:06:19 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r377772431  

I'll try to establish the true author, and absolve you of all possible blame for any hanging branches!

> Username: ckormanyos  
> Created_at: 2020-02-11 21:35:48 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r377913371  

> looks like @ckormanyos ... [wrote] it  
  
I did. I will look at the example and see if the variable is used or not.  
I will also check that example.

> Username: ckormanyos  
> Created_at: 2020-02-15 21:37:36 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r379857687  

>>> There also seem to be some branches in the logic which do not set `first_laguerre_root_has_been_found`?  
  
Correct. Thanks. Good catch. I have refactored these issues.  
  
>>I'll try to establish the true author  
  
> I will look at the example and see if the variable is used or not.  
  
I have refactored the example Gauss Laguerre quadrature  
[here](http://github.com/boostorg/multiprecision/tree/gauss_laguerre)

> Username: ckormanyos  
> Created_at: 2020-02-16 19:06:26 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r379925547  

See #196  
We can merge those changes to this PR if desired.


---

## Review 10 [Commented]

> Username: jzmaddock  
> Created_at: 2020-02-07 19:23:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/193#pullrequestreview-355382246  

📁 test/Jamfile.v2

```diff
  62 |-    
  61 |+    <toolset>clang-win:<link>static  # Clang-win does not generate .dlls.
  62 |+    <toolset>clang:<link>static # Clang-linux does not generate .dlls.
```

> Username: jzmaddock  
> Created_at: 2020-02-07 19:23:22 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r376564360  

As above this does not apply to non-windows platforms.


---

## Comment 11

> Username: pabristow  
> Created_at: 2020-02-13 14:34:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#issuecomment-585785206  

I’m still getting my head right around this example.  
  
I concluded that the warnings were correct and that a mid-point of the first estimate was unassigned, as you suggest.  
  
But the range of alpha was not used, so it didn’t make any difference.  
  
I note that the BOOST_CONSTEXPR, BOOST_STATIC_CONSTEXPR, BOOST_CONSTEXPR_OR_CONST  MACROS could be used?  
  
https://www.boost.org/doc/libs/1_72_0/libs/config/doc/html/boost_config/boost_macro_reference.html  
  
This might nicely exercise constexpr with multiprecision?  
  
It would be nice to convert to Quickbook snippets (and to reference from Boost.Math docs as well as a good example of using root finding.  
  
Tell what you would like me to do.  
  
  
  
  
  
  
  
  
From: Christopher Kormanyos <notifications@github.com>  
Sent: 11 February 2020 21:36  
To: boostorg/multiprecision <multiprecision@noreply.github.com>  
Cc: Paul A. Bristow <paul@pbristow.uk>; Comment <comment@noreply.github.com>  
Subject: Re: [boostorg/multiprecision] Documentation update (#193)  
  
  
@ckormanyos commented on this pull request.  
  
________________________________  
  
In example/gauss_laguerre_quadrature.cpp<https://github.com/boostorg/multiprecision/pull/193#discussion_r377913371>:  
  
> @@ -225,7 +225,7 @@ class guass_laguerre_abscissas_and_weights : private boost::noncopyable  
  
         // bisection steps in order to tighten up the root bracket.  
  
         boost::uintmax_t a_couple_of_iterations = 3U;  
  
         const std::pair<T, T>  
  
-          first_laguerre_root = boost::math::tools::bisect(laguerre_object,  
  
+          first_laguerre_roots = boost::math::tools::bisect(laguerre_object,  
  
looks like @ckormanyos<https://github.com/ckormanyos> ... [wrote] it  
  
I did. I will look at the example and see if the variable is used or not.  
I will also check that example.  
  
—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/multiprecision/pull/193?email_source=notifications&email_token=AAIG4AOTKRFZBTGJLQPEAWDRCMK3LA5CNFSM4KRS3CXKYY3PNVWWK3TUL52HS4DFWFIHK3DMKJSXC5LFON2FEZLWNFSXPKTDN5WW2ZLOORPWSZGOCVDZQAI#discussion_r377913371>, or unsubscribe<https://github.com/notifications/unsubscribe-auth/AAIG4APAGSD62UBXE5ZEK5DRCMK3LANCNFSM4KRS3CXA>.

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2020-02-13 21:46:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#issuecomment-585987622  

> I will look at the example and see if the variable is used or not.  
  
I am at the moment quite busy. I will attempt to check out the example on the weekend.  
Best regards, Chris

---

## Review 13 [Commented]

> Username: jzmaddock  
> Created_at: 2020-02-29 18:35:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/193#pullrequestreview-366812802  

📁 doc/multiprecision.qbk

```diff
1109 | * When compiling with `gcc`, you need to use the flag `--std=gnu++11/14/17`, as the suffix 'Q' is a GNU extension. Compilation fails with the flag `--std=c++11/14/17`
1110 | unless you also use `-fext-numeric-literals`.
1111 |+ * You will need to link to `libquadmath.dll` with the link command `-lquadmath` and ensure that the DLL is visible by the linker.
```

> Username: jzmaddock  
> Created_at: 2020-02-29 18:35:38 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r386046664  

I worry this is too windows centric?

> Username: ckormanyos  
> Created_at: 2020-02-29 18:55:35 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r386047945  

> I worry this is too windows centric?  
  
As it stands, it's not quite correct because you will not be linking to the DLL.  
  
One option would be to use a more vague, yet very simplified generic form:  
  
- You need to link with `quadmath` and also ensure that symbols from the quadmath library are available.  
  
Another option would be to describe more precisely for Win* or *nix separately.  
  
- On `*nix` systems, you need to link with `quadmath` by providing the linker flag `-lquadmath`, which will link with `libquadmath.a`.  
- On `Win*` systems such as GCC ported to MinGW, you also need to provide the linker with the `-lquadmath` flag in order to link with `libquadmath.lib`. It might also be necessary to ensure that symbols from the quadmath DLL are available at runtime.

> Username: jzmaddock  
> Created_at: 2020-02-29 19:00:48 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r386048248  

Perfect!  Glad one of us has a brain this evening! :)


---

## Review 14 [Commented]

> Username: jzmaddock  
> Created_at: 2020-02-29 18:36:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/193#pullrequestreview-366812848  

📁 doc/multiprecision.qbk

```diff
1110 | unless you also use `-fext-numeric-literals`.
1111 |+ * You will need to link to `libquadmath.dll` with the link command `-lquadmath` and ensure that the DLL is visible by the linker. 
1112 |+ If you are using the B2/bjam build system then commands`<linkflags>-lQUADMATH` and `<linkflags>-L"path/to/lib"` will be needed.
```

> Username: jzmaddock  
> Created_at: 2020-02-29 18:36:29 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r386046725  

QUADMATH in upper case will *not* work on Linux, it should be all lower case on both systems.

> Username: pabristow  
> Created_at: 2020-03-01 12:31:55 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r386104267  

Corrected to lower case.


---

## Review 15 [Commented]

> Username: jzmaddock  
> Created_at: 2020-02-29 18:37:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/193#pullrequestreview-366812883  

📁 doc/multiprecision.qbk

```diff
1112 |+ If you are using the B2/bjam build system then commands`<linkflags>-lQUADMATH` and `<linkflags>-L"path/to/lib"` will be needed.
1113 |+ * The values shown by `std::numeric_limits<float128>` and extremely close ['but not identical]
1114 |+ to those from the equivalent precision and range multiprecision types `std::numeric_limits<cpp_bin_float_quad>` and `std::numeric_limits<cpp_dec_float_quad>`.
```

> Username: jzmaddock  
> Created_at: 2020-02-29 18:37:08 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r386046770  

Actually, I hope they should be exactly the same, I haven't checked though have you?

> Username: pabristow  
> Created_at: 2020-03-01 15:20:00 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r386116377  

When I previously checked a few, some appeared a bit different in the 'noisy' digits.  I have rechecked and now understand that this was an aberration caused by a previous difference in max_digits10 (now both max_digits10=36) and one display showing trailing zeros, and the other not.  (support for std::hex would help with this).  
  
Now looks exact, but I haven't done an exhaustive check.   (should I) So now changed to:  
  
* The values shown by `std::numeric_limits<float128>` are the same as those from the equivalent precision and range multiprecision types `std::numeric_limits<cpp_bin_float_quad>` and `std::numeric_limits<cpp_dec_float_quad>`.  
  
I also propose to add an example program and include a listing all the numeric limits values for a variety of types, including these of course.  I feel users will find these convenient.


---

## Review 16 [Commented]

> Username: jzmaddock  
> Created_at: 2020-02-29 18:38:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/193#pullrequestreview-366812972  

📁 doc/multiprecision.qbk

```diff
1222 | `mpfi_float_500`, `mpfi_float_1000` provide arithmetic types at 50, 100, 500 and 1000 decimal digits precision
1178 |- respectively.  The `typedef mpfi_float` provides a variable precision type whose precision can be controlled via the
1223 |+ respectively.  The `typedef mpfi_float` provides a variable precision type whose precision can be controlled via theF
```

> Username: jzmaddock  
> Created_at: 2020-02-29 18:38:58 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r386046942  

Fat fingered 'F' ? ;)

> Username: pabristow  
> Created_at: 2020-03-01 12:34:27 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r386104425  

Fixed (but not the Fat Fingers :-( ).


---

## Review 17 [Commented]

> Username: jzmaddock  
> Created_at: 2020-02-29 18:42:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/193#pullrequestreview-366813134  

📁 doc/multiprecision.qbk

```diff
2160 |- Note that these make use of the numeric constants from the Math library, which also happen to be `constexpr`.
2207 |+ Note that these make use of the numeric constants from the __math_constants library, which also happen to be `constexpr`. 
2208 |+ These usually have the full precision of the floating-point type, here 128-bit, about 36 decimal digits.
```

> Username: jzmaddock  
> Created_at: 2020-02-29 18:42:12 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r386047134  

Hopefully, they should always have the full precision of the FT type?

> Username: pabristow  
> Created_at: 2020-03-01 12:36:33 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r386104579  

These should always have the full precision of the floating-point type, here 128-bit, about 36 decimal digits.


---

## Review 18 [Commented]

> Username: jzmaddock  
> Created_at: 2020-02-29 18:51:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/193#pullrequestreview-366813652  

📁 example/big_seventh.cpp

```diff
 107 |+ 
 108 |+ The 'random' digits after digit 17 are from the `cpp_bin_float_50` representation of the `double` value 0.14285714285714
 109 |+ which is different from the 'better' `cpp_bin_float_50` representation of the fraction 1/7
```

> Username: jzmaddock  
> Created_at: 2020-02-29 18:51:50 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r386047747  

This is a bit clunky but I don't know how to re-phrase - the cpp_bin_float_50 represents the double precision value exactly, but of course all the extra bits are set to zero.  The apparently "random" digits in the decimal representation aren't random at all - they're an artefact of the base-2 to 10 conversion (ie we can never represent the binary value exactly in base 10, so the decimal digits go on and on forever).  Apologies for being a pedant!

> Username: pabristow  
> Created_at: 2020-03-01 12:53:42 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r386105834  

Is this any better (if even clunkier?)     
  
The 'apparently random' digits after digit 17 are from the `cpp_bin_float_50` representation   
of the `double` value 0.14285714285714 that is, in effect, converted   
from 0.1428571428571400000000000000000000000000000000000  
which is different from the 'nearest representable' or 'best' `cpp_bin_float_50` representation of the fraction 1/7  
  
PS "The term, then, is obviously a relative one: my pedantry is your scholarship, his reasonable accuracy, her irreducible minimum of education and someone else’s ignorance."―H. W. Fowler, Modern English Usage.


---

## Review 19 [Commented]

> Username: jzmaddock  
> Created_at: 2020-02-29 18:54:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/193#pullrequestreview-366813783  

📁 example/big_seventh.cpp

```diff
 151 |+   std::cout << "cpp_bin_float_50 f2(\"1.234\") = " << f2 << std::endl; // 12.340000000000000000000000000000000000000000000000
 152 |+ 
 153 |+ /*`that are different from constructing from a `double` with value 1.234
```

> Username: jzmaddock  
> Created_at: 2020-02-29 18:54:10 UTC  
> Updated_at: 2020-03-19 09:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#discussion_r386047879  

... "since any binary floating point type only represents decimal fractions approximately."


---

## Comment 20

> Username: jzmaddock  
> Created_at: 2020-03-09 16:20:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#issuecomment-596629941  

Apologies @pabristow ball dropping again, is there anything more to do here?

---

## Comment 21

> Username: pabristow  
> Created_at: 2020-03-09 17:23:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#issuecomment-596666643  

No - you haven't dropped the ball - I'm holding it up!  
  
I believe I have dealt with all your comments.  
  
I have this on hold waiting to merge with some update to add the Gauss-Lagrange example before merging the whole docs-update into Multiprecision.  I'll check out what Chris has written or agree something about it.

---

## Comment 22

> Username: ckormanyos  
> Created_at: 2020-03-09 19:20:17 UTC  
> Updated_at: 2020-03-09 19:23:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#issuecomment-596732758  

>  I'll check out what Chris has written...  
  
The Gauss-Laguerre example benefited from #193   
  
Details we could work into the docs can be found in [lines 8 to 30 here](https://github.com/boostorg/multiprecision/blob/d2182773ac356de6e3a5e70afbc8f7bc48e33efa/example/gauss_laguerre_quadrature.cpp#L8)

---

## Comment 23

> Username: ckormanyos  
> Created_at: 2020-03-09 19:22:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#issuecomment-596733546  

> I'll check out what Chris has written  
  
The Gauss-Laguerre example is now better thanks to this PR.  
  
More details we can work into docs can be found in [lines 8 to 30 here](https://github.com/boostorg/multiprecision/blob/d2182773ac356de6e3a5e70afbc8f7bc48e33efa/example/gauss_laguerre_quadrature.cpp#L8)

---

## Comment 24

> Username: ckormanyos  
> Created_at: 2020-03-09 20:08:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#issuecomment-596754034  

> The Gauss-Laguerre example is now better thanks to this PR.  
  
I have locally cherry picked Gauss-Laguerre example. Can commit any time if you would like me to.  
  
Would you like me to merge and push the improved Gauss-Laguerre example cpp file from my branch into into your branch?  
  
Kind regards, Chris

---

## Comment 25

> Username: pabristow  
> Created_at: 2020-03-10 09:21:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#issuecomment-596983246  

Yes - please do that.  Then I will use the text you suggest to add to the .qbk, links to the example etc, and recheck.  I'll then merge docs updates into develop.

---

## Comment 26

> Username: ckormanyos  
> Created_at: 2020-03-10 19:27:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#issuecomment-597270704  

> Yes - please do that. Then I will use the text you suggest to add to the .qbk, links to the example etc, and recheck. I'll then merge docs updates into develop.  
  
Done. Thank you.  
Please continue with docs.  
  
Comments are scattered throughout the example, some are potentially fit for doc purposes.  
  
Especially [here](https://github.com/boostorg/multiprecision/blob/28c16704be1f6e558c4177abb5b7cf26bac644fc/example/gauss_laguerre_quadrature.cpp#L8)  
  
and [here](https://github.com/boostorg/multiprecision/blob/28c16704be1f6e558c4177abb5b7cf26bac644fc/example/gauss_laguerre_quadrature.cpp#L480)

---

## Review 27 [Commented]

> Username: pabristow  
> Created_at: 2020-03-11 09:31:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/193#pullrequestreview-372594116  

@ckormanyos Looks good - I'll work on addition to docs as ask for your review.

---

## Comment 28

> Username: ckormanyos  
> Created_at: 2020-03-11 19:21:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#issuecomment-597821762  

> I'll work on addition to docs  
  
Great. Ready when you are, Paul.

---

## Comment 29

> Username: madhur4127  
> Created_at: 2020-03-16 07:20:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#issuecomment-599381797  

Here's from [TODO](https://www.boost.org/doc/libs/1_71_0/libs/multiprecision/doc/html/boost_multiprecision/map/todo.html):  
  
> Add better multiplication routines (Karatsuba, FFT etc) to cpp_int_backend.  
  
Karatsuba is done and I think we discussed that FFT would not be beneficial as it has crossover point ~million limbs.  
  
> Add assembly level routines to cpp_int_backend.  
  
It seems we've rejected this proposal.  
  
@jzmaddock, @ckormanyos should I add the things that John pointed out like modular exponentiation, sqrt, gcd to this? Or we can use Github's Project as Mateusz Loskot does for [GIL](https://github.com/boostorg/gil/projects)

---

## Comment 30

> Username: ckormanyos  
> Created_at: 2020-03-16 19:13:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#issuecomment-599713822  

To your first points, Madhur...  
  
>> Add better multiplication routines (Karatsuba, FFT etc) to cpp_int_backend.  
  
> Karatsuba is done and I think we discussed that FFT would not be beneficial as it has crossover point ~million limbs.  
  
Actually, I would still like to mention these in the following form:  
Add better multiplication routines (Toom-Cook, FFT) to cpp_int_backend.  
  
>> Add assembly level routines to cpp_int_backend.  
  
> It seems we've rejected this proposal.  
  
Correct.

---

## Comment 31

> Username: ckormanyos  
> Created_at: 2020-03-18 21:53:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/193#issuecomment-600877601  

>  Then I will use the text you suggest to add to the .qbk, links to the example etc, and recheck. I'll then merge docs updates into develop.  
  
The part on Gauss-Laguerre example looks good to me. Thank you.

---
