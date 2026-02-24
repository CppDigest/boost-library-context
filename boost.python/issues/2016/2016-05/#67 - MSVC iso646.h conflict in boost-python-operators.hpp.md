# #67 - MSVC iso646.h conflict in boost/python/operators.hpp [Open]

> Username: jaynus  
> Created at: 2016-05-16 22:45:22 UTC  
> Updated at: 2019-04-16 17:30:34 UTC  
> Url: https://github.com/boostorg/python/issues/67  

MSVC includes iso646.h under various circumstances, which is easily worked around and is actually seen elsewhere in MPL (Boost issue https://svn.boost.org/trac/boost/ticket/3018). This applies strictly to MSVC, and when iso646.h is included from various means. The trac specifically said use github for Boost.Python related issues, so here I am.  
  
iso646.h defines various bitwise macros which are conflicting with the python operators (or, and, xor).  
  
This is easily fixed with prefix/postfix push_macro and pop_macro's for the appropriate functions.  
  
I have attached a diff against 1.61.  
  
[operators.patch.txt](https://github.com/boostorg/python/files/267087/operators.patch.txt)

---

## Comment 1

> Username: dzhoshkun  
> Created at: 2019-04-12 09:35:42 UTC  
> Url: https://github.com/boostorg/python/issues/67#issuecomment-482508428  

I'm experiencing this issue on Windows 10 with:  
  
* Visual Studio 2017 Community  
* Python 3.7  
* Boost 1.69  
  
I've tried applying the patch above, but the issue persists.  
  
@jaynus Am I missing something in the way [I'm applying the patch](https://github.com/gift-surg/GIFT-Grab/blob/fix-boost-python-numpy-discovery/src/python/wrapper.cpp#L28)?  
@stefanseefeld This issue seems to be quite old, but still open, so I'm guessing it's still valid?

---

## Comment 2

> Username: jaynus  
> Created at: 2019-04-16 17:30:34 UTC  
> Url: https://github.com/boostorg/python/issues/67#issuecomment-483769881  

@dzhoshkun  It looks correct to me. I haven't applied this patch to a current project in over 3 years though, so I can't say whats changed in a couple versions of visual studio.
