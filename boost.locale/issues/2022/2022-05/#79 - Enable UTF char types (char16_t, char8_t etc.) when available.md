# #79 - Enable UTF char types (char16_t, char8_t etc.) when available [Open]

> Username: Flamefire  
> Created at: 2022-05-25 17:38:22 UTC  
> Updated at: 2023-08-22 16:02:33 UTC  
> Url: https://github.com/boostorg/locale/issues/79  

Currently there are defines like `BOOST_LOCALE_ENABLE_CHAR16_T` to enable `char16_t` and similar for others.  
  
I find that a bit... difficult. We have `BOOST_NO_CHAR16_T` and similar to detect the availability automatically. So the defines are usually not required when that is used.  
  
And the major obstacle: The library must be build with the same value (defined / not-defined) of those `BOOST_LOCALE_ENABLE_CHAR*_T` defines or the locale facets will have undefined IDs. But there is no way to know how the library was built.  
  
Using the availability-test-macros one can assume, that if the library was built with a recent-ish compiler/standard then those types can be used.  
  
So @artyom-beilis : Switch to using `BOOST_NO_CHAR16_T` and the like?

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2022-05-25 18:27:40 UTC  
> Url: https://github.com/boostorg/locale/issues/79#issuecomment-1137693353  

> So @artyom-beilis : Switch to using `BOOST_NO_CHAR16_T` and the like?  
  
It is problem. And I'll explain.  
  
I have added `char16_t/char32_t` experimental support under assumption it is going to be useful. In reality it **never** worked. There are two reasons  
  
1. In order to char16/32 locales to work need at least most basic facets support from `std::locale` facets, for example there should be stuff like `std::ctype<char16_t>::id` exists (instantiated) somewhere in standard library. It never happened, also not sure it have ever been requirement from standard.  
2. Standard library facets like `ctype` or `numpunct`, `collate` in all existing implementations use support that comes from standard C library. For example on Linux/Mac the `libstdc++` locales use C `locale_l` functions/posix locale that exists in the library. For same reason under BSD and MinGW32 C++ locales aren't really supported. So, if you look into C++ library it can use some OS localisation support for Windows or Linux APIs. It is defined from wide char and char but have never been defined for `char16_t` or `char32_t`. So there is simply no streight forward way for library vendors to implement one, especially for "non-native" character width (16 for linux/32 for windows).  
  
So basically even I tried to implement it, the support was VERY limited and never worked. That is why you just can't use `BOOST_NO_CHAR16_T` since even if `char16_t` or `char32_t` are defined and work as separate type it does not guarantee that there is any support of it on library level.  
  
Finally I think it can be safely said that there chars aren't supported and will not be in near future.  
  
Now regarding compilation. It is Boost problem in general. Virtually every non-negligible library can be binary broken with some defines (even stuff like smart pointers).  
  
Since Boost decided not to use generated headers it is impossible to do this sync without systematic policy change in Boost. It isn't Boost.Locale's problem but rather major Boost design flaw.

---

## Comment 2

> Username: Flamefire  
> Created at: 2022-05-25 18:36:27 UTC  
> Url: https://github.com/boostorg/locale/issues/79#issuecomment-1137701076  

> Finally I think it can be safely said that there chars aren't supported and will not be in near future.  
>   
> Now regarding compilation. It is Boost problem in general. Virtually every non-negligible library can be binary broken with some defines (even stuff like smart pointers).  
  
If it never worked, is not expected to work and may cause problems, then let's remove that stuff. Less code to maintain, less trouble for users.

---

## Comment 3

> Username: artyom-beilis  
> Created at: 2022-05-25 18:49:59 UTC  
> Url: https://github.com/boostorg/locale/issues/79#issuecomment-1137719055  

> > Finally I think it can be safely said that there chars aren't supported and will not be in near future.  
> > Now regarding compilation. It is Boost problem in general. Virtually every non-negligible library can be binary broken with some defines (even stuff like smart pointers).  
>   
> If it never worked, is not expected to work and may cause problems, then let's remove that stuff. Less code to maintain, less trouble for users.  
  
I have no problem with it.

---

## Comment 4

> Username: Flamefire  
> Created at: 2023-08-22 16:02:33 UTC  
> Url: https://github.com/boostorg/locale/issues/79#issuecomment-1688492325  

Upon closer inspection and after the recent issue I think the better way forward is to enable those templates (unconditionally, i.e. remove the dependency on `BOOST_LOCALE_ENABLE_CHAR16_T` and the like)  
  
Additionally it also makes sense to enable `char8_t` when available.  
  
As seen in #190 users may expect to simply work with "type-encoded-UTF-encoding" when they want. And it is hard to argue why e.g. `from_utf<char8_t>` or `to_utf<char16_t>` shouldn't work while e.g. `utf8_codecvt<char16_t>` and `utf_to_utf<char, char16_t>` work
