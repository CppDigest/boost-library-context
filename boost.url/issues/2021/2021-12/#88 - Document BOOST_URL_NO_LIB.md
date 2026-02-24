# #88 - Document BOOST_URL_NO_LIB [Closed]

> Username: vinniefalco  
> Created at: 2021-12-16 19:53:02 UTC  
> Updated at: 2022-03-12 00:15:53 UTC  
> Closed at: 2022-03-12 00:15:53 UTC  
> Url: https://github.com/boostorg/url/issues/88  

The "header-only" configuration needs `BOOST_URL_NO_LIB` defined when building with MSVC

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-02-24 20:09:29 UTC  
> Updated at: 2022-02-24 20:36:13 UTC  
> Url: https://github.com/boostorg/url/issues/88#issuecomment-1050220615  

By "header-only", you mean when the user includes `#include <boost/url/src.hpp>` in some source file / translation unit, right? If this is header-only, what's _not_ header-only? If it's never header-only, do we always need `BOOST_URL_NO_LIB`, even if it's    
"`BOOST_URL_YES_LIB`"?  
  
I'm finding this naming convention a little confusing because `<boost/url/src.hpp>` is not really header-only at all, and the documentation often refers to that mode as header-only without any quotation marks.   
  
> "Alternatively you can use the header-only configuration simply by including this header file in exactly one of your new or existing source files"   
  
And quotation marks would also be worse than describing what things literally are without quotation marks anyway. Some libraries, like Asio, really support both modes. So even if "header-only" did always have quotation marks, it's still confusing, because it might imply the library supports "real" header-only and this "header-only" mode with quotation marks.  
  
Maybe it would be more reasonable to just document this like any other boost library that requires linking.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-02-24 21:25:14 UTC  
> Url: https://github.com/boostorg/url/issues/88#issuecomment-1050279480  

MSVC has "auto-linking" which can only be turned off by defining BOOST_URL_NO_LIB. I use the term "header-only" for marketing purposes. I think the term "header-only" is still applicable, as to use the library in this mode requires only the inclusion of header files.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-02-24 21:40:01 UTC  
> Url: https://github.com/boostorg/url/issues/88#issuecomment-1050291795  

> I use the term "header-only" for marketing purposes.   
  
Yes. I understood that. I don't care that much about libraries being header-only but I understand why many people would care.  
  
> I think the term "header-only" is still applicable, as to use the library in this mode requires only the inclusion of header files.  
  
I don't think I would use the term "header-only" in this case, but that's OK. That's not the real problem here.  
  
> MSVC has "auto-linking" which can only be turned off by defining BOOST_URL_NO_LIB.   
  
The problem here is I'm confused, probably because the marketing and the technical terms are in conflict. We're asking the user to set a macro `*_NO_LIB` for something that is `*_YES_LIB`.  
  
If including `src.hpp` is considered header-only, I don't know what's considered _not_ header-only. So I don't know when to tell the user _not_ to define `BOOST_URL_NO_LIB`.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-02-24 23:44:01 UTC  
> Url: https://github.com/boostorg/url/issues/88#issuecomment-1050368753  

you avoid defining `BOOST_URL_NO_LIB` when you want to link to the lib, e.g. "libboost_url-vc142-mt-sgd-x64-1_78.lib." But if you are including src.hpp then you aren't linking to the lib you are including the sources directly, thus you must define `BOOST_URL_NO_LIB` or else Boost.Config will try to activate auto-linking on MSVC.
