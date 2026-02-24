# #39 Proposal/no cxx14 macros [Closed]

> Username: Flast  
> Created at: 2014-09-22 16:03:24 UTC  
> Updated at: 2014-10-26 06:16:49 UTC  
> Closed at: 2014-10-12 16:09:36 UTC  
> Url: https://github.com/boostorg/config/pull/39  

I also propose BOOST_RELAXED_CONSTEXPR, which will help Fusion's constexpr supports: [Test output: BP x86_64 C++11 - fusion - swap / clang-linux-3.4~c11_libc++](http://www.boost.org/development/tests/develop/developer/output/BP%20x86_64%20C++11-boost-bin-v2-libs-fusion-test-swap-test-clang-linux-3-4~c11_libc++-debug-debug-symbols-off.html)  
  
New macros:  
- BOOST_NO_CXX14_BINARY_LITERALS  
- BOOST_NO_CXX14_RETURN_TYPE_DEDUCTION  
- BOOST_NO_CXX14_DECLTYPE_AUTO  
- BOOST_NO_CXX14_INITIALIZED_LAMBDA_CAPTURE  
- BOOST_NO_CXX14_GENERIC_LAMBDAS  
- BOOST_NO_CXX14_DEPRECATED_ATTRIBUTES  
- BOOST_NO_CXX14_DIGIT_SEPARATOR  
- BOOST_NO_CXX14_VARIABLE_TEMPLATES  
- BOOST_NO_CXX14_RELAXED_CONSTEXPR  
- BOOST_NO_CXX14_MEMBER_INITIALIZERS_AND_AGGREGATES  
- BOOST_NO_CXX14_SIZED_DEALLOCATION  
- BOOST_RELAXED_CONSTEXPR

---

## Comment 1

> Username: mgaunard  
> Created_at: 2014-09-25 11:10:11 UTC  
> Url: https://github.com/boostorg/config/pull/39#discussion_r18025814  

isn't return-type deduction also supported in C++11 as an extension?  
Also, what's the practical difference between N3386 and N3638 apart from decltype(auto)?

---

## Comment 2

> Username: Flast  
> Created_at: 2014-09-25 13:27:14 UTC  
> Url: https://github.com/boostorg/config/pull/39#discussion_r18031056  

> isn't return-type deduction also supported in C++11 as an extension?  
  
Ah, yes. GCC accepts return type deduction for normal functions as an extension. However, it seems that other macros (e.g. c++11 variadic templates) are not defined with -std=gnu++03.  
http://melpon.org/wandbox/permlink/LxU8OfRbucN4buru  
Then, I think new macros also compliant such behaviour is the better way.  
  
> Also, what's the practical difference between N3386 and N3638 apart from decltype(auto)?  
  
[n3638](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2013/n3638.html) shows the change log from n3386, but I have no effective examples.  
Although we can define the macro with GCC < 4.8, we might have to add a new macro (e.g. BOOST_NO_CXX14_RETURN_TYPE_DEDUCTION_N3638).

---

## Comment 3

> Username: mgaunard  
> Created_at: 2014-09-25 13:35:40 UTC  
> Url: https://github.com/boostorg/config/pull/39#discussion_r18031543  

since return-type deduction is arguably one of the two big features of C++14 for designing template libraries (along with generic lambdas), I think it's important to make it available as early as possible to users.  
  
As a library author, I'd like it if GCC 4.8 was enough, in particular because 4.9 isn't as widely deployed in stable linux distributions.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2014-09-25 16:11:21 UTC  
> Url: https://github.com/boostorg/config/pull/39#discussion_r18041700  

> Ah, yes. GCC accepts return type deduction for normal functions  
> as an extension. However, it seems that other macros  
> (e.g. c++11 variadic templates) are not defined with -std=gnu++03.  
  
That's because those features don't really work in C++03 mode - some   
things do compile OK (albeit with lots of dire warnings from GCC) - but   
lots of things don't.  
  
> http://melpon.org/wandbox/permlink/LxU8OfRbucN4buru  
> Then, I think new macros also compliant such behaviour is the better way.  
>   
> > Also, what's the practical difference between N3386 and N3638 apart from decltype(auto)?  
>   
> [n3638](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2013/n3638.html) shows the change log from n3386, but I have no effective examples.  
> Although we can define the macro with GCC < 4.8, we might have to add a new macro (e.g. BOOST_NO_CXX14_RETURN_TYPE_DEDUCTION_N3638).  
  
Let's try to not add too many new macros please - especially as no Boost   
library needs any of these quite yet.

---

## Comment 5

> Username: gnzlbg  
> Created_at: 2014-10-07 14:16:16 UTC  
> Updated_at: 2014-10-07 14:17:55 UTC  
> Url: https://github.com/boostorg/config/pull/39#discussion_r18521486  

> Let's try to not add too many new macros please - especially as no Boost  
> library needs any of these quite yet.  
  
Boost.Fusion does. There is a recent mailing list post from @mgaunard complaining that Boost.Fusion's develop branch (which no-one should use for anything serious anyways) is broken in C++11 because it uses BOOST_CONSTEXPR to denote C++14 constexpr functions (it _really_ needs BOOST_RELAXED_CONSTEXPR).   
  
Either Boost.Config gets a way to differentiate between C++11 and C++14 constexpr, or Boost.Fusion will need to develop its own.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2014-10-07 17:39:05 UTC  
> Url: https://github.com/boostorg/config/pull/39#discussion_r18535557  

> > Let's try to not add too many new macros please - especially as no Boost  
> > library needs any of these quite yet.  
>   
> Boost.Fusion does. There is a recent mailing list post complaining that Boost.Fusion's develop branch (which no-one should use for anything serious anyways) is broken in C++11 because it uses BOOST_CONSTEXPR to denote C++14 constexpr functions.  
>   
> Either Boost.Config gets a way to differentiate between C++11 and C++14 constexpr, or Boost.Fusion will need to develop its own.  
  
We will definitely add a macro for C++14 constexpr, my comment above was   
related to different "flavors" of return type deduction.

---

## Comment 7

> Username: Flast  
> Created_at: 2014-10-08 01:07:57 UTC  
> Url: https://github.com/boostorg/config/pull/39#discussion_r18559900  

> Let's try to not add too many new macros please - especially as no Boost library needs any of these quite yet.  
  
Yes, I agreed.  
  
And we have no way to detect GCC 4.8 run under which mode (C++11 v.s. C++14): __cplusplus is same value on both mode! So, we should suppose all of C++14 features are not available GCC 4.8 or earlier.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2014-10-12 16:09:36 UTC  
> Url: https://github.com/boostorg/config/pull/39#issuecomment-58806997  

This now (mostly) in develop:  
  
I renamed a couple of macros to match SD6, likewise used SD6 feature detection in the compiler config files rather than just blindly #defining everything (even though nothing supports these yet it appears).  
  
Also left out BOOST_NO_CXX14_DEPRECATED_ATTRIBUTES and BOOST_NO_CXX14_SIZED_DEALLOCATION - if there's demand these can be added later.  
  
For GCC-4.8 I enabled those C++14 features which are available without warning in C++11 mode (binary literals and lambda captures). GCC-4.9 has stricter conformance testing so lambda captures are disabled in C++11 mode, but more things are enabled in C++14 mode which we can now detect.  
  
I hope this makes sense.  
  
Please note: there's no way I've completely tested every conceivable compiler option, so bug reports are as ever welcome.

---

## Comment 9

> Username: Flast  
> Created_at: 2014-10-12 18:31:25 UTC  
> Url: https://github.com/boostorg/config/pull/39#issuecomment-58815039  

> For GCC-4.8 I enabled those C++14 features which are available without warning in C++11 mode (binary literals and lambda captures).  
  
GCC 4.8 will warn such extensions with -pedantic (and make as a error with -pedantic-error): http://melpon.org/wandbox/permlink/qcFYqNVb4N9c9XZP .  
Is this acceptable?  
  
Others are fine with me.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2014-10-13 08:38:04 UTC  
> Url: https://github.com/boostorg/config/pull/39#issuecomment-58861890  

> GCC 4.8 will warn such extensions with -pedantic (and make as a error with -pedantic-error): http://melpon.org/wandbox/permlink/qcFYqNVb4N9c9XZP .  
> Is this acceptable?  
  
No not really, but we can't detect when -pedantic is in effect (likewise   
-Werror or similar).  I'm in two minds about this, but my inclination is   
to leave as is for now and see how it works out in practice?

---

## Comment 11

> Username: Flast  
> Created_at: 2014-10-14 04:13:23 UTC  
> Updated_at: 2014-10-14 04:14:47 UTC  
> Url: https://github.com/boostorg/config/pull/39#issuecomment-58988142  

> I'm in two minds about this, but my inclination is to leave as is for now and see how it works out in practice?  
  
OK. I agree with you.

---

## Comment 12

> Username: morinmorin  
> Created_at: 2014-10-21 09:12:11 UTC  
> Url: https://github.com/boostorg/config/pull/39#issuecomment-59899724  

Sorry for being late to this discussion, but I have a few comments.  
  
1.  
  
```  
+#if __clang_major__ < 3 || (__clang_major__ == 3 && __clang_minor__ < 4)  
+# define BOOST_NO_CXX14_DIGIT_SEPARATOR  
+#endif  
```  
  
Please don't resort to the version check. If Clang does not have the feature querying macro yet,  
the best way is to first submit a patch to Clang (or file a bug report).   
I just submitted `cxx_digit_separators` patch to Clang;   
so let's wait until the patch is applied and then use the new feature querying macro!  
  
2.  
I think `BOOST_NO_CXX14_DIGIT_SEPARATOR` should be `BOOST_NO_CXX14_DIGIT_SEPARATORS`.  
  
P.S.  
Renaming of `BOOST_NO_CXX14_INITIALIZED_LAMBDA_CAPTURE` to  
`BOOST_NO_CXX14_INITIALIZED_LAMBDA_CAPTURES` does not apply to the doc.

---

## Comment 13

> Username: mgaunard  
> Created_at: 2014-10-21 09:28:38 UTC  
> Url: https://github.com/boostorg/config/pull/39#issuecomment-59901503  

Waiting for clang to have a feature querying macro might mean that you can't use the feature even though it's available in a previous version of clang.  
Clang has many versions each with its own bugs and incompatibilities, so you can't expect all users to be able to upgrade it to the latest version.

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2014-10-21 11:49:51 UTC  
> Url: https://github.com/boostorg/config/pull/39#issuecomment-59915677  

> 1.  
>   
> ```  
> +#if __clang_major__ < 3 || (__clang_major__ == 3 && __clang_minor__ < 4)  
> +# define BOOST_NO_CXX14_DIGIT_SEPARATOR  
> +#endif  
> ```  
>   
> Please don't resort to the version check. If Clang does not have the feature querying macro yet,  
> the best way is to first submit a patch to Clang (or file a bug report).  
> I just submitted `cxx_digit_separators` patch to Clang;  
> so let's wait until the patch is applied and then use the new feature querying macro!  
  
I agree with Mathias - the feature works perfectly well right now, no   
need to wait for a new clang version.  
  
> 2.  
> I think `BOOST_NO_CXX14_DIGIT_SEPARATOR` should be `BOOST_NO_CXX14_DIGIT_SEPARATORS`.  
  
I agree that looks better.  OK, I've changed this in develop and will   
merge to release post-beta.  
  
> P.S.  
> Renaming of `BOOST_NO_CXX14_INITIALIZED_LAMBDA_CAPTURE` to  
> `BOOST_NO_CXX14_INITIALIZED_LAMBDA_CAPTURES` does not apply to the doc.  
  
Also fixed in develop.  
  
Thanks, John.

---

## Comment 15

> Username: morinmorin  
> Created_at: 2014-10-21 13:36:16 UTC  
> Url: https://github.com/boostorg/config/pull/39#issuecomment-59927873  

Other vendors' Clang might use different version numbering system than that of  
the LLVM Project's Clang. For example, the newest Apple Clang' version  
number is 6.0.  
So this code `(__clang_major__ < 3) || (__clang_major__ == 3 &&  
__clang_minor__ < 4)`  
works differently on other vendors' Clang.  
  
John added `__cplusplus < 201400` check in develop/master after the  
merge of this PR.  
I think this check works reasonably well on other vendor's Clang too, because  
`__cplusplus` was changed from `201305L` to `201402L` after C++14  
digit separators  
was implemented.  
(On LLVM Project's Clang, this check implies the version < 3.5 and so  
the version check  
`(__clang_major__ < 3) || (__clang_major__ == 3 && __clang_minor__ <  
4)` makes no effect.)  
  
> I agree with Mathias - the feature works perfectly well right now, no  
> need to wait for a new clang version.  
  
OK. But please remove the version check and use `__cplusplus < 201400`  
(or `__cplusplus < 201402L`) alone.

---

## Comment 16

> Username: morinmorin  
> Created_at: 2014-10-21 13:42:05 UTC  
> Url: https://github.com/boostorg/config/pull/39#issuecomment-59928704  

Oops, the above comment doesn't show correctly.  
Here is a take two:  
  
Other vendors' Clang might use different version numbering system than that of   
the LLVM Project's Clang. For example, the newest Apple Clang' version number is 6.0.  
So this code `(__clang_major__ < 3) || (__clang_major__ == 3 && __clang_minor__ < 4)`  
works differently on other vendors' Clang.  
  
John added `__cplusplus < 201400` check in develop/master after the merge of this PR.  
I think this check works reasonably well on other vendor's Clang too, because   
`__cplusplus` was changed from `201305L` to `201402L` after C++14 digit separators   
was implemented.  
(On LLVM Project's Clang, this check implies the version < 3.5 and so the version check  
`(__clang_major__ < 3) || (__clang_major__ == 3 && __clang_minor__ < 4)` makes no effect.)  
  
> I agree with Mathias - the feature works perfectly well right now, no   
> need to wait for a new clang version.  
  
OK. But please remove the version check and use `__cplusplus < 201400`   
(or `__cplusplus < 201402L`) alone.

---

## Comment 17

> Username: Flast  
> Created_at: 2014-10-21 14:18:25 UTC  
> Url: https://github.com/boostorg/config/pull/39#issuecomment-59934322  

> For example, the newest Apple Clang' version number is 6.0.  
  
Does this mean Apple's clang define `__clang_major__` as `6` ?  
If it is truth, we should introduce normalized symbol, like a `BOOST_CLANG_VERSION`: clang's defects are not only this one.  
  
And should report this issue to Boost.Predef team?

---

## Comment 18

> Username: morinmorin  
> Created_at: 2014-10-21 15:30:35 UTC  
> Url: https://github.com/boostorg/config/pull/39#issuecomment-59946538  

> Does this mean Apple's clang define `__clang_major__` as `6` ?  
  
Yep.   
  
`__APPLE__` macro cannot be used to detect Apple Clang,   
since that macro is also defined on LLVM Project's Clang on OS X.  
  
`__VERSION__` macro contains the word "Apple" (as a sub-string),   
but it's just a string literal. I think it's not easy to detect   
Apple Clang using the string literal macro.  
  
Recent Apple Clang defines `__apple_build_version__` macro.   
We can detect recent Apple Clang using it.   
(Note: it is not defined on old Apple Clang. But, IMHO, old Apple Clang  
is not worth supporting. Anyway, it does not have C++14 feature support.)

---

## Comment 19

> Username: jzmaddock  
> Created_at: 2014-10-21 16:09:58 UTC  
> Url: https://github.com/boostorg/config/pull/39#issuecomment-59953241  

> Other vendors' Clang might use different version numbering system than that of  
> the LLVM Project's Clang. For example, the newest Apple Clang' version  
> number is 6.0.  
> So this code `(__clang_major__ < 3) || (__clang_major__ == 3 &&  
> __clang_minor__ < 4)`  
> works differently on other vendors' Clang.  
  
If true, I find that rather shocking actually :-(  
  
__has_feature is all very well, but is utterly useless for checking   
whether the feature is (reasonably) bug free.  For example in Kohei's   
constexpr fix:  
  
#if (**clang_major** == 3 && **clang_minor** < 5) \  
   || !(__has_feature(cxx_relaxed_constexpr) ||   
__has_extension(cxx_relaxed_constexpr))  
  
# define BOOST_NO_CXX14_CONSTEXPR  
  
#endif  
  
> OK. But please remove the version check and use `__cplusplus < 201400`  
> (or `__cplusplus < 201402L`) alone.  
  
I can do that for BOOST_NO_CXX14_DIGIT_SEPARATORS, but what about the   
issue above?  
  
John.

---

## Comment 20

> Username: morinmorin  
> Created_at: 2014-10-21 17:17:19 UTC  
> Url: https://github.com/boostorg/config/pull/39#issuecomment-59963275  

> __has_feature is all very well, but is utterly useless for checking   
> whether the feature is (reasonably) bug free.  
  
Agreed. Boolean `__has_feature(xxxx)` is useless to workaround   
compiler issues. I sometimes wish `__has_feature(xxxx)`   
(and SG6 feature tests) were versioned. For this purpose, I think   
it's OK to resort to some sort of version checks.  
  
> For example in Kohei's constexpr fix:  
>   
> ```  
> #if (__clang_major__ == 3 && __clang_minor__ < 5) \  
> || !(__has_feature(cxx_relaxed_constexpr) ||   
> __has_extension(cxx_relaxed_constexpr))  
> # define BOOST_NO_CXX14_CONSTEXPR  
> #endif  
> ```  
  
A.   
Though it's an ugly hack, `(__clang_major__ == 3 && __clang_minor__ < 5)` can be   
replaced by `!__has_feature(cxx_generic_lambdas)`.  
  
B.   
Use `(__clang_major__ == 3 && __clang_minor__ < 5)` on LLVM Project's Clang  
and use `__apple_build_version__` on Apple Clang?   
But what if there is another vendor's Clang...

---

## Comment 21

> Username: Flast  
> Created_at: 2014-10-22 01:16:41 UTC  
> Url: https://github.com/boostorg/config/pull/39#issuecomment-60023874  

> Though it's an ugly hack, `(__clang_major__ == 3 && __clang_minor__ < 5)` can be  
> replaced by `!__has_feature(cxx_generic_lambdas)`.  
  
Ah, it might achieve vendor free. But I think replacing `(__clang_major__ == 3 && __clang_minor__ < 5)` to `!__has_feature(cxx_generic_lambdas)` directly is not descriptive.  
Introducing a new macro `BOOST_CLANG_VERSION` looks good which is similar to libstdcpp3's one:   
https://github.com/boostorg/config/blob/develop/include/boost/config/stdlib/libstdcpp3.hpp#L124-L140 .

---

## Comment 22

> Username: jzmaddock  
> Created_at: 2014-10-22 17:01:04 UTC  
> Url: https://github.com/boostorg/config/pull/39#issuecomment-60118436  

> > Though it's an ugly hack, `(__clang_major__ == 3 && __clang_minor__ < 5)` can be  
> > replaced by `!__has_feature(cxx_generic_lambdas)`.  
>   
> Ah, it might achieve vendor free. But I think replacing `(__clang_major__ == 3 && __clang_minor__ < 5)` to `!__has_feature(cxx_generic_lambdas)` directly is not descriptive.  
> Introducing a new macro `BOOST_CLANG_VERSION` looks good which is similar to libstdcpp3's one:  
> https://github.com/boostorg/config/blob/develop/include/boost/config/stdlib/libstdcpp3.hpp#L124-L140 .  
  
It's too late for anything like that for the next release, so I went   
with some explanatory comments instead.  Plus I'm not sure we can set a   
BOOST_CLANG_VERSION macro based on feature detection as the features   
depend on what -std= command line is used as well as the compiler version.  
  
John.

---

## Comment 23

> Username: morinmorin  
> Created_at: 2014-10-23 15:58:45 UTC  
> Url: https://github.com/boostorg/config/pull/39#issuecomment-60262095  

## 1. SD-6 feature-test macro for C++14 digit separators  
  
N4030 (Feature-testing recommendations for C++) defines `__cpp_digit_separators` to be `201309`.  
http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n4030.htm  
  
## 2. Feature testing with SD-6 macros  
  
I think `!defined(__cpp_xxxx)` is unnecessary in `#if !defined(__cpp_xxxx) || (__cpp_xxxx < 201304)`. (Undefined identifiers are replaced with 0.)  
  
## 3. Fixes to clang.hpp  
  
I plan to make several fixes to `clang.hpp` in this weekend:  
- Remove `__has_extension` for features that are not available as extensions.  
- Remove `__has_extension` for features that emit warnings when used as extensions.  
- Replace `__has_feature` macros with SD-6 macros for C++14 features.  
- Replace `!__has_feature(cxx_generic_lambdas)` with `__cplusplus < 201402L` for C++14 constexpr.  
  
The first two fixes remove all `__has_extension` except the one for C++14 binary literals. The third fix has a bonus that we can use C++14 generic lambdas in Clang 3.4. (Generic lambdas were  implemented in Clang 3.4, but the `__has_feature` macro was slipped out.) The last fix is just a cosmetic one.

---

## Comment 24

> Username: jzmaddock  
> Created_at: 2014-10-23 18:14:35 UTC  
> Url: https://github.com/boostorg/config/pull/39#issuecomment-60283325  

> ## 1. SD-6 feature-test macro for C++14 digit separators  
>   
> N4030 (Feature-testing recommendations for C++) defines `__cpp_digit_separators` to be `201309`.  
> http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n4030.htm  
  
Understood, but it's not actually a part of SD6 yet?  
  
> ## 2. Feature testing with SD-6 macros  
>   
> I think `!defined(__cpp_xxxx)` is unnecessary in `#if !defined(__cpp_xxxx) || (__cpp_xxxx < 201304)`. (Undefined identifiers are replaced with 0.)  
  
Strictly speaking yes, but many compilers will warn if you check the   
value of an undefined macro, the !defined() shortcut suppresses this.  
  
> ## 3. Fixes to clang.hpp  
>   
> I plan to make several fixes to `clang.hpp` in this weekend:  
> - Remove `__has_extension` for features that are not available as extensions.  
> - Remove `__has_extension` for features that emit warnings when used as extensions.  
  
I guess this is OK.  
  
> - Replace `__has_feature` macros with SD-6 macros for C++14 features.  
  
Why?  Doesn't __has_feature work perfectly well, and are the SD6 macros   
actually implemented for all clang versions which support some C++14   
features?  
  
> - Replace `!__has_feature(cxx_generic_lambdas)` with `__cplusplus < 201402L` for C++14 constexpr.  
  
As long as it has the same effect and fixes Kohei's bug report I'm OK   
with that as long as it's carefully documented.  
  
BTW whatever changes are made now won't make the next release unless   
they're absolutely critical in some way (and even then may need special   
pleading).

---

## Comment 25

> Username: morinmorin  
> Created_at: 2014-10-26 03:51:56 UTC  
> Url: https://github.com/boostorg/config/pull/39#issuecomment-60505656  

> > ## 1. SD-6 feature-test macro for C++14 digit separators  
> >   
> > N4030 (Feature-testing recommendations for C++) defines `__cpp_digit_separators` to be `201309`.  
> > http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n4030.htm  
>   
> Understood, but it's not actually a part of SD6 yet?  
  
Hmm..., I don't know the status of SD6 in detail, but I think you're right.  
  
> > ## 2. Feature testing with SD-6 macros  
> >   
> > I think `!defined(__cpp_xxxx)` is unnecessary in `#if !defined(__cpp_xxxx) || (__cpp_xxxx < 201304)`. (Undefined identifiers are replaced with 0.)  
>   
> Strictly speaking yes, but many compilers will warn if you check the   
> value of an undefined macro, the !defined() shortcut suppresses this.  
  
Makes sense. Thanks for the explanation!  
  
> > - Replace `!__has_feature(cxx_generic_lambdas)` with `__cplusplus < 201402L` for C++14 constexpr.  
>   
> As long as it has the same effect and fixes Kohei's bug report I'm OK   
> with that as long as it's carefully documented.  
  
Yes, both of `__has_feature(cxx_generic_lambdas)` and `__cplusplus >= 201402L` mean that the code is compiled in C++14 (or C++1z) mode on Clang >= 3.5, and fixes Kohei's bug. I prefer `__cplusplus >= 201402L`, because `__has_feature(cxx_generic_lambdas)` might be confusing in the sense that C++14 generic lambdas are supported in Clang 3.4 but the `cxx_generic_lambdas` querying macro was introduced in Clang 3.5.  
  
> BTW whatever changes are made now won't make the next release unless   
> they're absolutely critical in some way (and even then may need special   
> pleading).  
  
OK, so the second fix in the list ("Remove `__has_extension` macros for features that emit warnings when used as extensions", i.e. remove `__has_extension(cxx_init_captures))` and `__has_extension(cxx_variable_templates)`) is the only candidate; if initialized lambda captures or variable templates are used in a C++11 mode with -Werror, we get compiler errors. I just pushed the fix to develop.

---

## Comment 26

> Username: morinmorin  
> Created_at: 2014-10-26 06:16:49 UTC  
> Url: https://github.com/boostorg/config/pull/39#issuecomment-60507835  

> > - Replace `__has_feature` macros with SD-6 macros for C++14 features.  
>   
> Why?  
  
It seems that Clang is moving towards preferring SD-6 macros. It is unclear whether Clang provide remaining `__has_feature` querying macros for C++14 features. So I thought that it was a good time to switch to use SD-6 macros.  
  
> Doesn't __has_feature work perfectly well,  
  
Yes, but SD-6 macros can detect generic lambdas and decltype(auto) on Clang-3.4 (`__has_feature(cxx_generic_lambdas)` and `__has_feature(cxx_decltype_auto)` need Clang 3.5).  
  
> and are the SD6 macros actually implemented for all clang versions which support  
> some C++14 features?  
  
I thought that `__has_feature` querying macros for C++14 features are supported from Clang-3.4, where Clang also implemented SD-6 macros. But it was wrong; `__has_feature` for "binary literals" and "aggregate nsdmi" were supported from Clang-3.3. Even, `__has_feature` macros support "contextual conversions", while there is no SD-6 macro for that feature.   
  
So, no. We should not blindly replace `__has_feature` macros with SD-6's. Sorry for making pre-mature suggestions and thanks for your comments.  
  
B.T.W. I checked when `__has_feature` and SD-6 feature-test macros have been introduced in Clang for each C++14 feature and put it into a gist: [C++14 macro status in Clang](https://gist.github.com/morinmorin/95f474a10228f91b7b47).

---
