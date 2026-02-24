# #122 support for IBM z/OS XL C/C++ (configure script & config headers) [Closed]

> Username: pgroke-dt  
> Created at: 2017-02-21 21:24:28 UTC  
> Updated at: 2017-05-04 08:06:14 UTC  
> Closed at: 2017-02-26 19:03:36 UTC  
> Url: https://github.com/boostorg/config/pull/122  

The changes provide at least some support for the IBM z/OS platform and the XL C/C++ compiler for z/OS. Far from perfect, but enough to use a fair subset of the Boost libraries.  
  
The changes to the configure script are necessary to make configure work on z/OS -- the "tr" tool of the "z/OS UNIX System Services" doesn't understand character ranges.  
  
The changes to the configure tests were necessary to avoid spurious successes.  
  
The rest, I think, should be clear.

---

## Comment 1

> Username: pgroke-dt  
> Created_at: 2017-02-21 21:47:42 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-281492016  

ps: I have made symmetric changes in "configure" and "configure.in" instead of changing only "configure.in" and re-running the Autotools. Mainly because I know very very little about the Autotools, and also because I didn't want to introduce a zillion changes by re-creating "configure" from scratch with a current Autotools package. Probably not the prettiest solution, but I think/hope it might be OK. If not, I'm open for suggestions.

---

## Review 2 [Changes requested]

> Username: glenfe  
> Created_at: 2017-02-21 23:25:07 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/config/pull/122#pullrequestreview-23100002  

In addition to where you now define BOOST_NO_SFINAE_EXPR can you also please define BOOST_NO_CXX11_SFINAE_EXPR

---

## Comment 3

> Username: pgroke-dt  
> Created_at: 2017-02-21 23:45:35 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-281519961  

Sure, done. Thank you, I've overlooked that.

---

## Comment 4

> Username: Lastique  
> Created_at: 2017-02-24 20:57:18 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282402454  

Library-specific macros like `BOOST_SP_NO_SYNC` and `BOOST_DATE_TIME_NO_LOCALE` don't belong to Boost.Config. They should be added to the respective libraries by separate patches.  
  
I'm not sure defining `__IBMCPP_DEFAULTED_AND_DELETED_FUNCTIONS` is a good idea. If that macro is not defined by the compiler now, it may be defined in the future. And `__IBMCPP_*` namespace does not belong to Boost anyway, so we shouldn't define such macros. If you know the defaulted/deleted functions are supported, just check the compiler version and/or other compiler-defined macros and define the corresponding `BOOST_NO_CXX11_*` macros straight away.  
  
Also, I don't think `BOOST_XLCPP_ZOS_DO_NOT_GUESS` is necessary. The feature either works (confirmed with tests) or it doesn't, it is Boost.Config's job to tell that without external help. If the feature may work in some configurations but not others, with no way to detect it, I'd rather mark it unavailable unconditionally.

---

## Comment 5

> Username: glenfe  
> Created_at: 2017-02-24 23:53:05 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282437014  

Would it be better  if BOOST_ALIGNMENT definitions were all either in implementation specific headers or all in the one suffix header? I just noticed that now, with your change, it would be in both.

---

## Review 6 [Commented]

> Username: jzmaddock  
> Created_at: 2017-02-25 13:03:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/122#pullrequestreview-23857765  

📁 include/boost/config/compiler/xlcpp_zos.hpp

```diff
  14 |+ #endif
  15 |+ 
  16 |+ #if __COMPILER_VER__ > 0x42010000
```

> Username: jzmaddock  
> Created_at: 2017-02-25 13:03:42 UTC  
> Updated_at: 2017-02-26 18:38:56 UTC  
> Url: https://github.com/boostorg/config/pull/122#discussion_r103078486  

We don't normally have hard failures just because the compiler has been updated, there may be new features in the new compiler that we don't detect, but that's normally safe anyway.  Please wrap this in a #ifdef BOOST_ASSERT_CONFIG block.  Thanks!

> Username: pgroke-dt  
> Created_at: 2017-02-25 22:12:16 UTC  
> Updated_at: 2017-02-26 18:38:56 UTC  
> Url: https://github.com/boostorg/config/pull/122#discussion_r103089767  

I don't understand. Isn't the next line exactly what you want?


---

## Review 7 [Commented]

> Username: jzmaddock  
> Created_at: 2017-02-25 13:05:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/122#pullrequestreview-23857794  

📁 include/boost/config/compiler/xlcpp_zos.hpp

```diff
  54 |+ //   Guess: defaulted and deleted functions feature is active if ALL other C++11 features supported by the compiler are active.
  55 |+ #    if !defined(__IBMCPP_DEFAULTED_AND_DELETED_FUNCTIONS)
  56 |+ #      define __IBMCPP_DEFAULTED_AND_DELETED_FUNCTIONS 1
```

> Username: jzmaddock  
> Created_at: 2017-02-25 13:05:58 UTC  
> Updated_at: 2017-02-26 18:38:56 UTC  
> Url: https://github.com/boostorg/config/pull/122#discussion_r103078524  

As previously noted, I don't think we should be defining an IMB macro here, suggest you call it something else with a BOOST_ prefix, and then also set it when __IBMCPP_DEFAULTED_AND_DELETED_FUNCTIONS is set.

> Username: Lastique  
> Created_at: 2017-02-25 14:01:17 UTC  
> Updated_at: 2017-02-26 18:38:56 UTC  
> Url: https://github.com/boostorg/config/pull/122#discussion_r103079408  

If Boost.Config is to define a private macro, it would probably be better to use a private prefix for Boost.Config. `BOOST_CONFIG_DETAIL_` or something. I know it's long, that's why I suggested to define the public macros right away.

> Username: pgroke-dt  
> Created_at: 2017-02-25 22:33:31 UTC  
> Updated_at: 2017-02-26 18:38:56 UTC  
> Url: https://github.com/boostorg/config/pull/122#discussion_r103090220  

I have though about this again, and I think it would be OK to just write the code as if `__IBMCPP_DEFAULTED_AND_DELETED_FUNCTIONS` worked correctly. Worst thing that can happen then is that Boost.Config defines the three defect macros even though the compiler would support the feature. Which happens all the time e.g. when using a new compiler version with a Boost version that hasn't been updated yet. Shouldn't be a real problem.  
  
Also, if the user really needs to, he/she can probably simply define `__IBMCPP_DEFAULTED_AND_DELETED_FUNCTIONS` from the command line.


---

## Review 8 [Commented]

> Username: jzmaddock  
> Created_at: 2017-02-25 13:10:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/122#pullrequestreview-23857837  

📁 include/boost/config/select_compiler_config.hpp

```diff
  93 | #   define BOOST_COMPILER_CONFIG "boost/config/compiler/mpw.hpp"
  94 | 
  95 |+ #elif defined(__IBMCPP__) && defined(__COMPILER_VER__)
```

> Username: jzmaddock  
> Created_at: 2017-02-25 13:10:02 UTC  
> Updated_at: 2017-02-26 18:38:56 UTC  
> Url: https://github.com/boostorg/config/pull/122#discussion_r103078566  

I'm pretty sure those macros are common to all IMB C++ compliers, not just z/OS, we need a better check here, otherwise it will break IBM C++ on other platforms.

> Username: pgroke-dt  
> Created_at: 2017-02-25 22:17:32 UTC  
> Updated_at: 2017-02-26 18:38:56 UTC  
> Url: https://github.com/boostorg/config/pull/122#discussion_r103089884  

According to https://sourceforge.net/p/predef/wiki/Compilers/ the macro `__COMPILER_VER__` is only defined by the z/OS variant of XL C/C++. I could add `&& !defined(__xlc__) && !defined(__xlC__)` - those macros should only be defined for other XL C/C++ compilers.


---

## Review 9 [Commented]

> Username: jzmaddock  
> Created_at: 2017-02-25 13:28:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/122#pullrequestreview-23858098  

📁 tools/configure.in

```diff
 168 |    title=`cat $file | grep '^//[[ 	]]*TITLE:' | $SED 's/.*TITLE:[[ 	]]*\([[^ 	]].*\)/\1/'`
 169 |-    namespace=`echo $macroname | tr [[A-Z]] [[a-z]]`
 169 |+    namespace=`echo $macroname | tr $as_cr_LETTERS $as_cr_letters`
```

> Username: jzmaddock  
> Created_at: 2017-02-25 13:28:03 UTC  
> Updated_at: 2017-02-26 18:38:56 UTC  
> Url: https://github.com/boostorg/config/pull/122#discussion_r103078868  

Where is $as_cr_letters defined?

> Username: pgroke-dt  
> Created_at: 2017-02-25 22:20:40 UTC  
> Updated_at: 2017-02-26 18:38:56 UTC  
> Url: https://github.com/boostorg/config/pull/122#discussion_r103089930  

To be honest, I don't know. It somehow makes its way into the "configure" script - I guess it might be code inserted by the Autotools? Should I change this to `ABCDEFGHIJKLMNOPQRSTUVWXYZ` instead to avoid a dependency on wherever this comes from?


---

## Comment 10

> Username: pgroke-dt  
> Created_at: 2017-02-25 22:29:18 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282517248  

@Lastique   
Regarding `__IBMCPP_DEFAULTED_AND_DELETED_FUNCTIONS`...  
The z/OS compiler has a lot of switches by which support for various parts of the new C++11 standard can be turned on or off independently. One of those switches controls support for the "defaulted and deleted functions" feature. The documentation says that, if the switch is on, the feature detect macro `__IBMCPP_DEFAULTED_AND_DELETED_FUNCTIONS` will be defined. Only it isn't. I've compared a macro dump with the switch on and off, and they are 100% identical, so I don't see any way to detect if it's supported.

---

## Comment 11

> Username: pgroke-dt  
> Created_at: 2017-02-25 22:48:00 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282518272  

@glenfe   
Regarding `BOOST_ALIGNMENT`...  
I guess it would be better to only set this in the compiler config header. Same with `BOOST_RESTRICT`, `BOOST_FORCEINLINE`, `BOOST_NOINLINE` and `BOOST_NORETURN`.  
But I also think it's acceptable the way it is now. There's simple code in `suffix.hpp` that will work for many compilers, and if it doesn't, the compiler config header can override it.  
Only that for `BOOST_ALIGNMENT` it wasn't possible for the compiler config header to override, so I had to change that.  
  
Of course I could have extended the logic in `suffix.hpp`, but while I do think that the code in `suffix.hpp` is an acceptable legacy, I don't think it would be desirable to extend it further.

---

## Comment 12

> Username: Lastique  
> Created_at: 2017-02-26 00:02:00 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282521885  

On Sun, Feb 26, 2017 at 1:33 AM, Paul Groke <notifications@github.com>  
wrote:  
  
> I have though about this again, and I think it would be OK to just write  
> the code as if __IBMCPP_DEFAULTED_AND_DELETED_FUNCTIONS worked correctly.  
> Worst thing that can happen then is that Boost.Config defines the three  
> defect macros even though the compiler would support the feature. Which  
> happens all the time e.g. when using a new compiler version with a Boost  
> version that hasn't been updated yet. Shouldn't be a real problem.  
>  
> Also, if the user really needs to, he/she can probably simply define  
> __IBMCPP_DEFAULTED_AND_DELETED_FUNCTIONS from the command line.  
>  
Agreed.

---

## Comment 13

> Username: Lastique  
> Created_at: 2017-02-26 00:06:55 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282522087  

On Sun, Feb 26, 2017 at 1:48 AM, Paul Groke <notifications@github.com>  
wrote:  
  
> @glenfe <https://github.com/glenfe>  
> Regarding BOOST_ALIGNMENT...  
> I guess it would be better to only set this in the compiler config header.  
> Same with BOOST_RESTRICT, BOOST_FORCEINLINE, BOOST_NOINLINE and  
> BOOST_NORETURN.  
> But I also think it's acceptable the way it is now. There's simple code in  
> suffix.hpp that will work for many compilers, and if it doesn't, the  
> compiler config header can override it.  
> Only that for BOOST_ALIGNMENT it wasn't possible for the compiler config  
> header to override, so I had to change that.  
>  
> Of course I could have extended the logic in suffix.hpp, but while I do  
> think that the code in suffix.hpp is an acceptable legacy, I don't think  
> it would be desirable to extend it further.  
>  
The code in suffix.hpp is not legacy, some macros are intentionally defined  
there because they're similarly defined for many compilers. This mostly  
concerns GCC and MSVC pretenders implementing the same functionality with  
the same syntax. Your compiler seem to be compatible with the GCC syntax,  
even if it doesn't pretend to be GCC by defining `__GNUC__`.

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2017-02-26 09:33:57 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282544035  

On 25/02/2017 22:12, Paul Groke wrote:  
>  
> *@pgroke-dt* commented on this pull request.  
>  
> ------------------------------------------------------------------------  
>  
> In include/boost/config/compiler/xlcpp_zos.hpp   
> <https://github.com/boostorg/config/pull/122#discussion_r103089767>:  
>  
> > +//  Copyright (c) 2017 Dynatrace  
> +//  
> +//  Distributed under the Boost Software License, Version 1.0.  
> +//  See accompanying file LICENSE_1_0.txt or copy at  
> +//  http://www.boost.org/LICENSE_1_0.txt  
> +  
> +//  See http://www.boost.org for most recent version.  
> +  
> +//  Compiler setup for IBM z/OS XL C/C++ compiler.  
> +  
> +// Oldest compiler version currently supported is 2.1 (V2R1)  
> +#if !defined(__IBMCPP__) || !defined(__COMPILER_VER__) || __COMPILER_VER__ < 0x42010000  
> +#  error "Compiler not supported or configured - please reconfigure"  
> +#endif  
> +  
> +#if __COMPILER_VER__ > 0x42010000  
>  
> I don't understand. Isn't the next line exactly what you want?  
>  
  
Only if BOOST_ASSERT_CONFIG is defined: think about it, newer compilers   
will presumably be backwards compatible and still work with this   
configuration, they just may not enable every feature that could be enabled.

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2017-02-26 09:36:33 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282544154  

On 25/02/2017 22:17, Paul Groke wrote:  
>  
> *@pgroke-dt* commented on this pull request.  
>  
> ------------------------------------------------------------------------  
>  
> In include/boost/config/select_compiler_config.hpp   
> <https://github.com/boostorg/config/pull/122#discussion_r103089884>:  
>  
> > @@ -92,8 +92,12 @@  
>   //  MPW MrCpp or SCpp  
>   #   define BOOST_COMPILER_CONFIG "boost/config/compiler/mpw.hpp"  
>     
> +#elif defined(__IBMCPP__) && defined(__COMPILER_VER__)  
>  
> According to https://sourceforge.net/p/predef/wiki/Compilers/ the   
> macro |__COMPILER_VER__| is only defined by the z/OS variant of XL   
> C/C++. I could add |&& !defined(__xlc__) && !defined(__xlC__)| - those   
> macros should only be defined for other XL C/C++ compilers.  
>  
  
OK, maybe just add a !defined(|__MVS__) as belt and braces?  
|

---

## Comment 16

> Username: jzmaddock  
> Created_at: 2017-02-26 09:40:22 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282544319  

> ------------------------------------------------------------------------  
>  
> In tools/configure.in   
> <https://github.com/boostorg/config/pull/122#discussion_r103089930>:  
>  
> > @@ -166,7 +166,7 @@ for file in $boost_base/libs/config/test/boost_no*.ipp; do  
>      basename=`echo $file | $SED 's/.*boost_\(.*\)\.ipp/\1/'`  
>      macroname=`cat $file | grep '^//[[ 	]]*MACRO:' | $SED 's/.*MACRO:[[ 	]]*\([[_A-Z0-9]]*\).*/\1/'`  
>      title=`cat $file | grep '^//[[ 	]]*TITLE:' | $SED 's/.*TITLE:[[ 	]]*\([[^ 	]].*\)/\1/'`  
> -   namespace=`echo $macroname | tr [[A-Z]] [[a-z]]`  
> +   namespace=`echo $macroname | tr $as_cr_LETTERS $as_cr_letters`  
>  
> To be honest, I don't know. It somehow makes its way into the   
> "configure" script - I guess it might be code inserted by the   
> Autotools? Should I change this to |ABCDEFGHIJKLMNOPQRSTUVWXYZ|   
> instead to avoid a dependency on wherever this comes from?  
>  
  
A quick Google suggests it's an undocumented addition to all configure   
scripts, might be safer and more explicit to use   
|ABCDEFGHIJKLMNOPQRSTUVWXYZ instead.  Sorry to be pedantic about this!  
|

---

## Comment 17

> Username: jzmaddock  
> Created_at: 2017-02-26 09:41:17 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282544356  

> ------------------------------------------------------------------------  
>  
> In include/boost/config/compiler/xlcpp_zos.hpp   
> <https://github.com/boostorg/config/pull/122#discussion_r103090220>:  
>  
> > +    && defined(__IBMCPP_RVALUE_REFERENCES) \  
> +    && defined(__IBMCPP_RIGHT_ANGLE_BRACKET) \  
> +    && defined(__IBMCPP_REFERENCE_COLLAPSING) \  
> +    && defined(__IBMCPP_INLINE_NAMESPACE) \  
> +    && defined(__IBMCPP_EXTENDED_FRIEND) \  
> +    && defined(__IBMCPP_EXPLICIT_CONVERSION_OPERATORS) \  
> +    && defined(__IBMCPP_DELEGATING_CTORS) \  
> +    && defined(__IBMCPP_DECLTYPE) \  
> +    && defined(__IBMCPP_CONSTEXPR) \  
> +    && defined(__IBMCPP_AUTO_TYPEDEDUCTION) \  
> +   )  
> +//   According to documentation, the "defaulted and deleted functions" feature should be testable via the predefined macro __IBMCPP_DEFAULTED_AND_DELETED_FUNCTIONS.  
> +//   Unfortunately this doesn't work, the macro is actually never defined (compiler version V2R1).  
> +//   Guess: defaulted and deleted functions feature is active if ALL other C++11 features supported by the compiler are active.  
> +#    if !defined(__IBMCPP_DEFAULTED_AND_DELETED_FUNCTIONS)  
> +#      define __IBMCPP_DEFAULTED_AND_DELETED_FUNCTIONS 1  
>  
> I have though about this again, and I think it would be OK to just   
> write the code as if |__IBMCPP_DEFAULTED_AND_DELETED_FUNCTIONS| worked   
> correctly. Worst thing that can happen then is that Boost.Config   
> defines the three defect macros even though the compiler would support   
> the feature. Which happens all the time e.g. when using a new compiler   
> version with a Boost version that hasn't been updated yet. Shouldn't   
> be a real problem.  
>  
> Also, if the user really needs to, he/she can probably simply define   
> |__IBMCPP_DEFAULTED_AND_DELETED_FUNCTIONS| from the command line.  
>  
  
Agreed.  Plus IBM will presumably fix this at some point.

---

## Comment 18

> Username: Beman  
> Created_at: 2017-02-26 11:52:51 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282550727  

On Sun, Feb 26, 2017 at 4:41 AM, jzmaddock <notifications@github.com> wrote:  
...  
  
  
> > Also, if the user really needs to, he/she can probably simply define  
> > |__IBMCPP_DEFAULTED_AND_DELETED_FUNCTIONS| from the command line.  
> >  
>  
> Agreed. Plus IBM will presumably fix this at some point.  
>  
  
FWIW, one of the IBM z/OS standard library developers has started to attend  
C++ committee meetings and is working on an implementation of  
std::filesystem. I've got his name and email if you want it. It would make  
no sense for them to be working on std::filesystem unless they are also  
working on other aspects of C++17.  
  
--Beman

---

## Comment 19

> Username: pgroke-dt  
> Created_at: 2017-02-26 14:56:15 UTC  
> Updated_at: 2017-02-26 15:08:34 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282561389  

@Lastique  
  
> The code in suffix.hpp is not legacy, some macros are intentionally defined there because they're similarly defined for many compilers. This mostly concerns GCC and MSVC pretenders implementing the same functionality with the same syntax. Your compiler seem to be compatible with the GCC syntax, even if it doesn't pretend to be GCC by defining `__GNUC__`.  
  
In that case, I'd suggest leaving that part of my patch as it is :) I don't think a default handling in `suffix.hpp` without any way to "escape" it is desirable.  
  
> Library-specific macros like `BOOST_SP_NO_SYNC` and `BOOST_DATE_TIME_NO_LOCALE` don't belong to Boost.Config. They should be added to the respective libraries by separate patches.  
  
I've removed the definitions of those two macros. I've created a `BOOST_SP_NO_SYNC` patch for Boost.SmartPtr which has already been merged. Boost.DateTime uses `BOOST_WORKAROUND` when defining `BOOST_DATE_TIME_NO_LOCALE`, which doesn't yet work with z/OS XL C/C++. I'll probably create a PR for Boost.DateTime within the next few days/weeks.

---

## Comment 20

> Username: pgroke-dt  
> Created_at: 2017-02-26 15:22:28 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282563118  

@jzmaddock   
  
> Only if BOOST_ASSERT_CONFIG is defined: think about it, newer compilers will presumably be backwards compatible and still work with this configuration, they just may not enable every feature that could be enabled.  
  
The code in the compiler config file checks the compiler version, and issues an error if either  
- The compiler version is less then the one I've used  
- The compiler version is greater AND `BOOST_ASSERT_CONFIG` is defined (see the nested `if defined(BOOST_ASSERT_CONFIG)` in line 17)  
  
Which AIFAIK is exactly what other compiler config files do, and - if I understand correctly - already implements the behavior that you argue for.  
  
If this is a misunderstanding on my part, it would probably help if you showed me which change in which line(s) would implement the behavior that you mean.

---

## Comment 21

> Username: jzmaddock  
> Created_at: 2017-02-26 15:32:27 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282563878  

On 26/02/2017 15:22, Paul Groke wrote:  
>  
> @jzmaddock <https://github.com/jzmaddock>  
>  
>     Only if BOOST_ASSERT_CONFIG is defined: think about it, newer  
>     compilers will presumably be backwards compatible and still work  
>     with this configuration, they just may not enable every feature  
>     that could be enabled.  
>  
> The code in the compiler config file checks the compiler version, and   
> issues an error if either  
>  
>   * The compiler version is less then the one I've used  
>   * The compiler version is greater AND |BOOST_ASSERT_CONFIG| is  
>     defined (see the nested |if defined(BOOST_ASSERT_CONFIG)| in line 17)  
>  
> Which AIFAIK is exactly what other compiler config files do, and - if   
> I understand correctly - already implements the behavior that you   
> argue for.  
>  
> If this is a misunderstanding on my part, it would probably help if   
> you showed me which change in which line(s) would implement the   
> behavior that you mean.  
>  
  
My mistake!  Sorry for the noise on that, and yes IMO leaving the   
alignment code "as is" is fine.

---

## Comment 22

> Username: pgroke-dt  
> Created_at: 2017-02-26 15:38:39 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282564293  

@Beman   
Sounds interesting. Maybe you could send him a link to this discussion and/or tip him off to the `__IBMCPP_DEFAULTED_AND_DELETED_FUNCTIONS` "bug". I wouldn't mind having his email address either, but of course only if he's OK with that.

---

## Comment 23

> Username: Lastique  
> Created_at: 2017-02-26 15:39:23 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282564331  

On 02/26/17 17:56, Paul Groke wrote:  
> @Lastique <https://github.com/Lastique>  
>  
>     The code in suffix.hpp is not legacy, some macros are intentionally  
>     defined there because they're similarly defined for many compilers.  
>     This mostly concerns GCC and MSVC pretenders implementing the same  
>     functionality with the same syntax. Your compiler seem to be  
>     compatible with the GCC syntax, even if it doesn't pretend to be GCC  
>     by defining |__GNUC__|.  
>  
> In that case, I'd suggest leaving that part of my patch as it is :) I  
> don't think a default handling in |suffix.hpp| without any way to  
> "escape" it is desirable.  
  
I think, what Glen meant in his comment is that there is current one   
place where those macros are defined, suffix.hpp. Adding the "escape"   
would mean they may be defined in the compiler-specific headers as well,   
which makes it slightly less maintainable. That is also considering that   
the compiler supports GCC syntax, which means it should be easy enough   
to keep the definitions in suffix.hpp.  
  
Personally, I have no strong preference about this. It seems, in the   
long run, as the number of compiler checks in suffix.hpp increases, the   
definitions from that file should be moved to the respective compiler   
headers. I'm not sure if we reached that point yet; let's see what   
@jzmaddock has to say about it.

---

## Comment 24

> Username: pgroke-dt  
> Created_at: 2017-02-26 15:45:55 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282564816  

@Lastique   
@jzmaddock just wrote a couple of minutes ago   
  
> and yes IMO leaving the alignment code "as is" is fine  
  
;)  
Personally I also think that _any_ definition in `suffix.hpp` should have an escape mechanism - simply because it gives users the chance to override in `user.hpp`.

---

## Comment 25

> Username: pgroke-dt  
> Created_at: 2017-02-26 16:00:13 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282565727  

@jzmaddock   
OK, I've added `&& defined(__MVS__)` to the compiler detection condition.

---

## Comment 26

> Username: jzmaddock  
> Created_at: 2017-02-26 16:12:28 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282566602  

Thanks for all the changes, as far as I can tell this is good to go now, anyone else?

---

## Comment 27

> Username: Lastique  
> Created_at: 2017-02-26 16:55:54 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282569599  

I have no further comments.

---

## Comment 28

> Username: glenfe  
> Created_at: 2017-02-26 17:11:13 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282570677  

Looks fine to me.

---

## Comment 29

> Username: pgroke-dt  
> Created_at: 2017-02-26 18:42:06 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282576927  

Great!  
I just remembered that I used the same condition as was in `select_compiler_config.hpp` in `select_stdlib_config.hpp` as well. I've now added the `&& defined(__MVS__)` there too.

---

## Comment 30

> Username: jzmaddock  
> Created_at: 2017-02-26 19:03:36 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282578383  

Merged, thanks!

---

## Comment 31

> Username: pgroke-dt  
> Created_at: 2017-02-27 09:15:43 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282666375  

Cool. Thanks everybody for your time and your help!

---

## Comment 32

> Username: pgroke-dt  
> Created_at: 2017-02-27 09:21:51 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282667666  

One last question though: will this change be in 1.64 or in 1.65?

---

## Comment 33

> Username: jzmaddock  
> Created_at: 2017-02-27 09:30:49 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-282669818  

On 27/02/2017 09:21, Paul Groke wrote:  
>  
> One last question though: will this change be in 1.64 or in 1.65?  
>  
  
I'm waiting on some tests to cycle before merging to master, but I'm   
hoping for 1.64.

---

## Comment 34

> Username: pgroke-dt  
> Created_at: 2017-05-03 08:24:39 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-298850395  

Now... 1.64 is out. Any chance of merging this to master some time soon? I'm asking because those changes are prerequisites for PRs that I'd like to submit for Boost.Atomic and Boost.SmartPtr. I'm not sure what the policy regarding that is, but I personally would feel more comfortable if the necessary code in config were in master before I start submitting the other PRs...

---

## Comment 35

> Username: jzmaddock  
> Created_at: 2017-05-03 10:59:55 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-298880626  

On 03/05/2017 09:24, Paul Groke wrote:  
>  
> Now... 1.64 is out. Any chance of merging this to master some time   
> soon? I'm asking because those changes are prerequisites for PRs that   
> I'd like to submit for Boost.Atomic and Boost.SmartPtr. I'm not sure   
> what the policy regarding that is, but I personally would feel more   
> comfortable if the necessary code in config were in master before I   
> start submitting the other PRs...  
>  
  
Done.  Thanks for the reminder, John.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 36

> Username: pgroke-dt  
> Created_at: 2017-05-03 12:06:26 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-298892288  

Great, thanks!  
So the changes should already be in https://sourceforge.net/projects/boost/files/boost/snapshots/master/ or show up there soon, right?

---

## Comment 37

> Username: jzmaddock  
> Created_at: 2017-05-03 12:24:36 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-298895660  

On 03/05/2017 13:06, Paul Groke wrote:  
>  
> Great, thanks!  
> So the changes should already be in   
> https://sourceforge.net/projects/boost/files/boost/snapshots/master/   
> or show up there soon, right?  
>  
  
Nope, sourceforge is dead for us now, it's at:   
https://github.com/boostorg/config/commits/master  
  
John.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 38

> Username: pgroke-dt  
> Created_at: 2017-05-03 12:28:40 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-298896490  

I was looking for snapshots of the whole collection... didn't find any on github. And at first glance, the ones on sourceforge seem to be updated rather frequently ("boost_1_65_0-snapshot.zip 	< 1 hour ago")...

---

## Comment 39

> Username: glenfe  
> Created_at: 2017-05-03 18:11:04 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-298991050  

https://bintray.com/boostorg/master/snapshot#files might be what you're after.

---

## Comment 40

> Username: pgroke-dt  
> Created_at: 2017-05-04 08:06:14 UTC  
> Url: https://github.com/boostorg/config/pull/122#issuecomment-299121083  

Yes, thank you! :)

---
