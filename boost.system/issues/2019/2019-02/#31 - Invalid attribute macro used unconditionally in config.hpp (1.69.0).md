# #31 - Invalid attribute macro used unconditionally in config.hpp (1.69.0) [Closed]

> Username: Sil3ntStorm  
> Created at: 2019-02-06 21:35:35 UTC  
> Updated at: 2019-03-16 17:07:31 UTC  
> Closed at: 2019-03-16 17:07:31 UTC  
> Url: https://github.com/boostorg/system/issues/31  

[detail/config.hpp](https://github.com/boostorg/system/blob/develop/include/boost/system/detail/config.hpp#L51) unconditionally checks for a clang specific non-standard [language extension](https://clang.llvm.org/docs/AttributeReference.html#require-constant-initialization).  
This causes compilation to fail on non-clang compilers.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-02-06 21:40:19 UTC  
> Url: https://github.com/boostorg/system/issues/31#issuecomment-461198462  

What does "unconditionally checks" mean in this context? The whole point of `__has_cpp_attribute` is to check if the attribute is supported.

---

## Comment 2

> Username: Sil3ntStorm  
> Created at: 2019-02-06 21:51:11 UTC  
> Url: https://github.com/boostorg/system/issues/31#issuecomment-461202102  

The issue here is you're attempting to use `__has_cpp_attribute` with an argument that is clang specific (hence the clang:: prefix) on every compiler, rather than only when clang is being used (which would understand the non standard argument).  
Surrounding Lines 51 to 53 with an `#ifdef __clang__` `#endif` guard solves the compile issue on non clang compilers, while still allowing clang to take advantage of its extension.  
  
That section should probably look like this:  
```  
#if defined(__has_cpp_attribute)  
#if defined(__clang__)  
#if __has_cpp_attribute(clang::require_constant_initialization)  
# undef BOOST_SYSTEM_REQUIRE_CONST_INIT  
# define BOOST_SYSTEM_REQUIRE_CONST_INIT [[clang::require_constant_initialization]]  
#endif  
#endif  
#endif  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2019-02-06 22:02:48 UTC  
> Url: https://github.com/boostorg/system/issues/31#issuecomment-461205853  

What specific "non-Clang" compilers are we talking about here? `__has_cpp_attribute` is supposed to give 0 for arguments that it doesn't understand. What would its purpose be if it either returned 1 (for supported) or gave an error (for unsupported)?

---

## Comment 4

> Username: Sil3ntStorm  
> Created at: 2019-02-06 22:29:05 UTC  
> Url: https://github.com/boostorg/system/issues/31#issuecomment-461213770  

MSVC 19.20.27305   
So this would be a bug in the Microsoft compiler then?

---

## Comment 5

> Username: pdimov  
> Created at: 2019-02-06 22:34:26 UTC  
> Url: https://github.com/boostorg/system/issues/31#issuecomment-461215324  

That's how I read the standard, yes. http://eel.is/c++draft/cpp.cond#5  
  
I'll ifdef that out for MSVC when I verify that it breaks. It works on 2017, but it probably doesn't implement `__has_cpp_attribute`.

---

## Comment 6

> Username: Sil3ntStorm  
> Created at: 2019-02-06 23:05:57 UTC  
> Updated at: 2019-02-06 23:10:40 UTC  
> Url: https://github.com/boostorg/system/issues/31#issuecomment-461224203  

Thanks for the link. I guess they understood the "The program is ill-formed if the pp-tokens do not match the form of an attribute-token." as not including attribute-scoped-token.    
Especially since http://eel.is/c++draft/dcl.attr.grammar#6 also only explicitly mentions attribute-tokens are ignored while saying the behavior is implementation defined for anything not mentioned in the document.    
    
So I guess technically they're complying with the document, their implementation defined behavior is just less useful compared to other compilers, which [appear to just ignore it](https://godbolt.org/z/IkVFIF).  
  
And yes, the latest MSVC available on Godbolt doesn't seem to have `__has_cpp_attribute` yet. This is with the current 2019 preview.  
  
Sorry for the fuzz, should've checked other non-clang compilers first I guess.

---

## Comment 7

> Username: pdimov  
> Created at: 2019-02-06 23:44:40 UTC  
> Url: https://github.com/boostorg/system/issues/31#issuecomment-461233389  

attribute-token clearly does include attribute-scoped-token though, so I think they just have a bug here.  
  
```  
attribute-token:  
	identifier  
	attribute-scoped-token  
```  
  
Either way, if they continue erroring out on this, we'll have to work around it, but now we can put a comment explaining who is in the wrong. :-)

---

## Comment 8

> Username: pdimov  
> Created at: 2019-02-07 14:32:35 UTC  
> Url: https://github.com/boostorg/system/issues/31#issuecomment-461443166  

@mloskot reports that the Boost.System tests pass with VS 2019: https://gist.github.com/mloskot/efbfb2b33c99b8f70cfe4f912a8ec4d6

---

## Comment 9

> Username: pdimov  
> Created at: 2019-02-07 14:34:25 UTC  
> Url: https://github.com/boostorg/system/issues/31#issuecomment-461443977  

Maybe it only fails with `cxxstd=17` or with `cxxstd=latest`.

---

## Comment 10

> Username: Sil3ntStorm  
> Created at: 2019-02-07 17:29:42 UTC  
> Url: https://github.com/boostorg/system/issues/31#issuecomment-461522009  

Maybe only fails on 64 bit or debug builds for some reason? From the gist it looks like only 32 bit builds were done (never tried 32 bit myself).  
  
`/D "BOOST_REGEX_NO_LIB" /D "BOOST_DATE_TIME_NO_LIB" /D "BOOST_SYSTEM_NO_DEPRECATED" /D "BOOST_SYSTEM_NO_LIB" /std:c++17 /permissive-`  
  
This was in a project using Boost header only, mainly asio.  
Did not build Boost itself (or any part of it). Just the project failed because it was ultimately including the header file through boost headers. Doing the fix mentioned in the original report fixed the build issue for me, hence the suggestion.

---

## Comment 11

> Username: mloskot  
> Created at: 2019-02-07 17:52:57 UTC  
> Url: https://github.com/boostorg/system/issues/31#issuecomment-461530349  

@pdimov   
> Maybe it only fails with `cxxstd=17` or with `cxxstd=latest`.  
  
Added logs for the two https://gist.github.com/mloskot/efbfb2b33c99b8f70cfe4f912a8ec4d6

---

## Comment 12

> Username: pdimov  
> Created at: 2019-02-07 18:24:14 UTC  
> Url: https://github.com/boostorg/system/issues/31#issuecomment-461541222  

It's probably `/permissive-` then. Perhaps you (@Sil3ntStorm) should report that to MS, although they are already on RC so it probably won't be fixed before release.

---

## Comment 13

> Username: Sil3ntStorm  
> Created at: 2019-02-07 23:56:00 UTC  
> Url: https://github.com/boostorg/system/issues/31#issuecomment-461640619  

Actually I have had a chance to look into it again now, and it appears that this is not actually the compiler misbehaving but the Visual Studio IDE / IntelliSense.  
I was going through the list of errors, and this one was early in the list so I made it go away. Seeing how @mloskot compiled successfully I removed the extra ifdef I had added, and sure enough the error shows up in the list again, however compilation actually succeeds.  
Ultimately this being reported as an error in the Error List in Visual Studio, led me to the conclusion this was a real issue, when it seems it really isn't.

---

## Comment 14

> Username: pdimov  
> Created at: 2019-02-08 00:03:08 UTC  
> Url: https://github.com/boostorg/system/issues/31#issuecomment-461642184  

Maybe it's an Intellisense error, or a static analysis error? Those show up in the same list.

---

## Comment 15

> Username: Sil3ntStorm  
> Created at: 2019-02-08 00:11:52 UTC  
> Url: https://github.com/boostorg/system/issues/31#issuecomment-461643985  

code analysis is not enabled in the properties of the project as far as I can see. Unless there is a new option some place else that I am not aware of.
