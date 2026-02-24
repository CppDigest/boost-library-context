# #139 Fix Windows shared linking tests [Closed]

> Username: res2k  
> Created at: 2018-11-13 16:58:30 UTC  
> Updated at: 2018-11-19 18:05:14 UTC  
> Closed at: 2018-11-17 18:55:04 UTC  
> Url: https://github.com/boostorg/serialization/pull/139  

* Only tag symbols as dll-exported when building a DLL  
* Only tag symbols as dll-imported when using a DLL  
* Don't define DLL-imported method  
* Add missing dependency

---

## Comment 1

> Username: robertramey  
> Created_at: 2018-11-17 18:55:04 UTC  
> Url: https://github.com/boostorg/serialization/pull/139#issuecomment-439639355  

I wasn't crazy about your method here of adding yet another macro.  I resolved this by slightly restructuring code to better separate declaration from definition.

---

## Comment 2

> Username: res2k  
> Created_at: 2018-11-18 17:15:33 UTC  
> Updated_at: 2018-11-18 17:39:43 UTC  
> Url: https://github.com/boostorg/serialization/pull/139#issuecomment-439709070  

I added Yet Another Macro to make it work on MinGW as well.  
  
The MinGW linker doesn't like it if you use a symbol with a `dllimport` attribute in one object, but with a `dllexport` attribute in another; the latter doesn't define the `__imp_*` symbols that are expected.  
(The MSVC linker sucks this up, but emits a warning LNK4217.)  
Thus you need to have an `dllexport` attribute for on the symbol definition that goes into the DLL, and _no_ `dll*` attribute for the object file that will be used "statically". (Or have the symbol also appear as `dllexport` in places it's used as well; that's how you use a `dllexport` symbol from the DLL itself.)  
  
FWIW, the typical approach for DLLs on Windows is having macros "are we using a shared FOO?" and "are we building FOO?".  
The extra macro was the former, and was used to not have `dll*` attributes in the case that `polymorphic_derived2` is linked statically. Currently, you can't link `polymorphic_derived2` statically while using shared Boost.Serialization.

---

## Comment 3

> Username: robertramey  
> Created_at: 2018-11-18 19:04:13 UTC  
> Url: https://github.com/boostorg/serialization/pull/139#issuecomment-439716911  

hmm - I'm thinking you're on to something - but I need a better explanation to understand this.  
  
I presume you're referring to the module named dll_polymorphic_derived2.  
  
>Thus you need to have an dllexport attribute for on the symbol definition that goes into the DLL, and no dll* attribute for the object file that will be used "statically". (Or have the symbol also appear as dllexport in places it's used as well; that's how you use a dllexport symbol from the DLL itself.)  
  
I'm thinking that this is a quirk of the gcc compiler.  Generally it does not require "visibility" for imported symbols declared in a header.  It only requires visibility when those symbols exported from another module.   As you note - windows does it differently. So wouldn't the "correct" way to handle this to be that for this platform - mingw - , POLYMORPHIC_BASE_DLL_DECL be defined as nothing for imported symbols and BOOST_SYMBOL_EXPORT for exported symbols?

---

## Comment 4

> Username: res2k  
> Created_at: 2018-11-19 16:45:43 UTC  
> Url: https://github.com/boostorg/serialization/pull/139#issuecomment-439960531  

> So wouldn't the "correct" way to handle this to be that for this platform - mingw - , POLYMORPHIC_BASE_DLL_DECL be defined as nothing for imported symbols and BOOST_SYMBOL_EXPORT for exported symbols?  
  
Yes, that works, for both MinGW and MSVC. (Test results at https://github.com/res2k/serialization/pull/13)  
  
The difference with omitting `dllimport` (on functions) is that the compiler can't use the hint to generate slightly more efficient code (see: http://blogs.msdn.com/oldnewthing/archive/2006/07/24/676669.aspx), but for a test that's probably acceptable anyway. Semantically there is no difference.  
  
(Sidenote: Notice the "on functions". If you happen to `dllexport` variables, a `dllimport` on the using side is essential.)  
  
BTW, if you're interested in in-depth information on DLL importing & exporting, I can recommend these articles: https://blogs.msdn.microsoft.com/oldnewthing/20060727-04/

---

## Comment 5

> Username: robertramey  
> Created_at: 2018-11-19 17:03:14 UTC  
> Url: https://github.com/boostorg/serialization/pull/139#issuecomment-439966830  

Hmm - I'm surprised that removing the dllimport on the calling program doesn't break the compile on msvc platforms.  To see the whole point was to only be able to invoke functions that one explicitly imported. I'm very aware of the MS dll/com/... import export thing going back more that 20 years.  It's a ill thought out hack and has always been so.  The gcc - visibility isn't much better.  And of course the C++ committee spends all it's time on stuff other than things like this which are and always have been a problem.  
  
But enough of the rant.  You've demonstrated the issue.  But I think the fix in in the wrong place.  Fixing in the test is "hiding the bug".  It makes the test pass, but it doesn't help for the next time.  Think bigger! Take a look at this excerpt from polymorphic_derived2.hpp:  
  
`#if defined(BOOST_ALL_DYN_LINK) || defined(BOOST_SERIALIZATION_DYN_LINK)  
    #if defined(POLYMORPHIC_DERIVED2_IMPORT)  
        #define POLYMORPHIC_DERIVED2_DLL_DECL BOOST_SYMBOL_IMPORT  
        #pragma message ("polymorphic_derived2 imported")  
    #elif defined(POLYMORPHIC_DERIVED2_EXPORT)  
        #define POLYMORPHIC_DERIVED2_DLL_DECL BOOST_SYMBOL_EXPORT  
        #pragma message ("polymorphic_derived2 exported")  
    #endif  
#endif  
`  
To me it seems that the real solution is that the value of BOOST_SYMBOL_IMPORT should be defined to nothing for the MINGW platform.  Following up on this will almost surely lead to config.hpp.  This is John Maddox's territory.  I asked him about this on either the user or developer's list and he was at a loss.  So I'd suggest that you finish nailing this down and propose a definitive fix at the source.  I'm sure he'd be appreciative.  And you'd have the satisfaction of not fixing one test in the serialization library, but fixing for now and forever any boost application which uses boost, mingw and DLL.  That's what I mean by thinking bigger!

---

## Comment 6

> Username: res2k  
> Created_at: 2018-11-19 17:52:11 UTC  
> Url: https://github.com/boostorg/serialization/pull/139#issuecomment-439983113  

Hmm, one issue would be that `dllimport` is required for variables. Just setting `BOOST_SYMBOL_IMPORT` to empty would instantly break any users of variables across modules. Sure, you can work around that by having a function returning a reference, but that may penalize other platforms.  
  
Also, an empty `BOOST_SYMBOL_IMPORT` would inhibit the supposed efficiency gain on Windows, however small that is. I could imagine that fear of a performance decline would have some users prefer a retention of `dllimport`.  
  
So, an empty `BOOST_SYMBOL_IMPORT` would probably not be an universal improvement.

---

## Comment 7

> Username: robertramey  
> Created_at: 2018-11-19 17:57:33 UTC  
> Url: https://github.com/boostorg/serialization/pull/139#issuecomment-439984833  

> So, an empty BOOST_SYMBOL_IMPORT would probably not be an universal improvement.  
  
If you look at how config.hpp works, you'll see that these macros are defined by the combination of compiler and the OS.  So one would expect to "fine tune" the definition of BOOST_SYMBOL_IMPORT so that it would be defined as dll_import on plain windows and leave it undefined on MINGW.  This of course would be the optimal solution.  As the issue that windoze/mingw makes it even more complex by having different rules for imported variables than for imported functions ... what can I say.  Nothing surprise me any more.  In any case, of hand I don't know of a way to handle this.

---

## Comment 8

> Username: res2k  
> Created_at: 2018-11-19 18:05:14 UTC  
> Url: https://github.com/boostorg/serialization/pull/139#issuecomment-439987288  

Looking back at having macros like `FOO_DYN_LINK` and `FOO_SOURCE` controling a `FOO_DECL` macro, there's plenty of precedence for that, including generally Boost libs themselves.  
Even if we're dealing with mere test helper libraries here, applying the pattern doesn't feel that out of place.  
It may even have the benefit that an observer unfamiliar with the pecularities of `dllimport` and `dllexport` can find a recognizable pattern instead of maybe having to wonder "why does no BOOST_SYMBOL_IMPORT even work?".  
Of course, the latter could be somewhat remedied by a comment explaining why, in this particular case, that seemingly non-obvious solution was chosen.

---
