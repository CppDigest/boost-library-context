# #696 - boost/url/src.hpp must be included before other boost/url headers to make boost/url header-only: documentation change needed [Closed]

> Username: GrahamAsher  
> Created at: 2023-02-23 15:00:39 UTC  
> Updated at: 2023-03-01 22:21:40 UTC  
> Closed at: 2023-03-01 22:21:40 UTC  
> Url: https://github.com/boostorg/url/issues/696  

I used this code in a project compiled using Microsoft Visual Studio 2022 (Microsoft Visual Studio Community 2022 (64-bit) - Version 17.4.5):  
  
```  
#include <boost/url/parse.hpp>  
#include <boost/url/src.hpp>  
```  
  
and got a link error stating that a library was required. The documentation says that header-only use of boost/url can be achieved if boost/url/src.hpp is included. It should be amended to say that boost/url/src.hpp has to be included before any other boost/url files. I verified this by changing my code to  
  
```  
#include <boost/url/src.hpp>  
#include <boost/url/parse.hpp>  
```  
  
which prevented the link error from occurring.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-02-23 16:22:35 UTC  
> Url: https://github.com/boostorg/url/issues/696#issuecomment-1442065124  

You also have to define BOOST_URL_NO_LIB if you are using Visual Studio. Then, the order won't matter.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2023-02-23 16:23:30 UTC  
> Url: https://github.com/boostorg/url/issues/696#issuecomment-1442066547  

The documentation should say "Visual Studio users must also define the macro `BOOST_URL_NO_LIB` to disable auto-linking"

---

## Comment 3

> Username: GrahamAsher  
> Created at: 2023-02-23 16:39:59 UTC  
> Url: https://github.com/boostorg/url/issues/696#issuecomment-1442089731  

Hi Vinnie, thank you for acknowledging this problem. It's much easier to use header-only libraries, so in general the way to do that should be made as clear as possible.

---

## Comment 4

> Username: alandefreitas  
> Created at: 2023-02-23 19:47:16 UTC  
> Url: https://github.com/boostorg/url/issues/696#issuecomment-1442337549  

> "Visual Studio users must also define the macro BOOST_URL_NO_LIB to disable auto-linking"  
  
"MSVC users must also define the macro BOOST_URL_NO_LIB to disable auto-linking"?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2023-02-23 20:09:18 UTC  
> Updated at: 2023-02-23 20:09:27 UTC  
> Url: https://github.com/boostorg/url/issues/696#issuecomment-1442367922  

Yeah that sounds good. Next time don't ask me just do it.

---

## Comment 6

> Username: alandefreitas  
> Created at: 2023-03-01 22:21:40 UTC  
> Url: https://github.com/boostorg/url/issues/696#issuecomment-1450933800  

I just checked and the documentation already says that in much more detail. I just remembered we had a long discussion a long time ago where we came up with a template to use in boost.url and other libs.  
  
As this issue indicates, we **could** mention in the docs that things will also work when you forget `BOOST_URL_NO_LIB` and include `#include <boost/url/src.hpp>` before anything else because it also enforces `BOOST_URL_NO_LIB`.  
  
But that would only make things more confusing and incentivize bad behavior.
