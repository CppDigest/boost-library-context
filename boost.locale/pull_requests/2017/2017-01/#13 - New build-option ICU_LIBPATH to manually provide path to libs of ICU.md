# #13 New build-option ICU_LIBPATH to manually provide path to libs of ICU. [Closed]

> Username: DenizThatMenace  
> Created at: 2017-01-05 10:54:41 UTC  
> Updated at: 2017-07-13 15:02:55 UTC  
> Closed at: 2017-07-13 13:52:09 UTC  
> Url: https://github.com/boostorg/locale/pull/13  

When using _Boost.Build_ to build _**Boost.Locale**_ one can build and link with the external library **ICU** and provide the path to its (root-)directory using the option `ICU_PATH`. The ICU library files are searched in a sub-directory `lib` (or in some situations in `lib64`) of _`<ICU_PATH>`_.  
However, that only works if the library files are directly located in `<ICU_PATH>/lib` (or `<ICU_PATH>/lib64`).  
  
If they are installed in a custom location,  
* either a totally different location,  
* another named sub-directory (e.g. `<ICU_PATH>/libraries` ) or  
* even in a sub-directory of the expected sub-directory (e.g. `<ICU_PATH>/lib/x86_64-linux-gnu`),  
  
this does no longer work and the ICU libs cannot be found.  
  
Therefore, this pull-request provides an additional _Boost.Build_ option `ICU_LIBPATH` with which one can provide the path to the directory where the **ICU** libraries are located.

---

## Comment 1

> Username: artyom-beilis  
> Created_at: 2017-07-13 13:46:20 UTC  
> Url: https://github.com/boostorg/locale/pull/13#issuecomment-315082364  

You can use ICU_LINK option directly and provide whatever link flags you like.

---

## Comment 2

> Username: DenizThatMenace  
> Created_at: 2017-07-13 14:37:10 UTC  
> Url: https://github.com/boostorg/locale/pull/13#issuecomment-315097161  

The associated [pull-request to *Boost.Regex*](https://github.com/boostorg/regex/pull/27) should probably be treated the same as this pull-request (aka being closed without a merge).  
  
(Hpwever, note that I did not test if the same irritating messages from *Boost.Build* occur as for *Boost.Regex* when using `ICU_LINK` with *Boost.Locale*.)

---

## Comment 3

> Username: artyom-beilis  
> Created_at: 2017-07-13 15:00:56 UTC  
> Updated_at: 2017-07-13 15:02:55 UTC  
> Url: https://github.com/boostorg/locale/pull/13#issuecomment-315104625  

The biggest issue is that Boost.Build is broken system that is very hard to maintain. So if normal workaround exists like providing ICU_LINK it is preferred.  
  
The correct fix is to provide a search path for generic includes and generic libraries on BB level regardless ICU as it shoudn't be specific to ICU only.  
  
CMake has search path for both libraries and includes and works much smoother but in BB such a trivial task is very problematic.  
  
So utill Boost migrates to cmake I prefer not to tinker with BB as it is virtually impossible to test/maintain.

---
