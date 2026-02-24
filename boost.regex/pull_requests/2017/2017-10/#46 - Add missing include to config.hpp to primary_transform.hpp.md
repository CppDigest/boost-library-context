# #46 Add missing include to config.hpp to primary_transform.hpp [Closed]

> Username: Teemperor  
> Created at: 2017-10-24 11:51:45 UTC  
> Updated at: 2017-10-31 13:24:52 UTC  
> Closed at: 2017-10-25 17:10:18 UTC  
> Url: https://github.com/boostorg/regex/pull/46  

This header declares a namespace with the BOOST_REGEX_DETAIL_NS  
token from the config.hpp header, but never includes this header  
unlike the other headers in this directory.  
This means that if a user includes this header first, suddenly  
all declarations are inside the literal 'BOOST_REGEX_DETAIL_NS'  
namespace which breaks all code using this header.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-10-25 17:10:18 UTC  
> Url: https://github.com/boostorg/regex/pull/46#issuecomment-339401658  

The short answer is "don't do that".  None of the headers under boost/regex/v4 are intended for direct inclusion by the end user, and most will simply not function if done so.

---

## Comment 2

> Username: Teemperor  
> Created_at: 2017-10-26 06:20:38 UTC  
> Url: https://github.com/boostorg/regex/pull/46#issuecomment-339563385  

To give more context: This PR is C++ modules related. You can read more about modules [here](https://clang.llvm.org/docs/Modules.html) or [here](https://www.youtube.com/results?search_query=c%2B%2B+modules). I'm working on the clang C++ modules for the boost libraries and this header is currently causing problems when precompiling it into a submodule.   
  
In short: A header should be standalone if possible so that we can precompile it into a module. If that's not possible, it should  
* at least generate a compiler error when getting parsed  
* have no header guards  
* have a `.ipp` file extension.  
  
This header is currently parsing without any errors into a completely wrong AST as described above.  
  
It would make my life a easier if we can get this header standalone by just adding this missing include. Or give it a `.ipp` extension or whatever works for you in case you really don't want this header to be standalone. I would prefer the include because more modular headers make the modules smaller/faster.

---

## Comment 3

> Username: vgvassilev  
> Created_at: 2017-10-31 13:24:52 UTC  
> Url: https://github.com/boostorg/regex/pull/46#issuecomment-340760867  

@jzmaddock, is there any chance to reconsider? This blocks the clang c++ modules support for [CMSSW](https://github.com/cms-sw/cmssw/issues/15248#issuecomment-234206190) unless we pile up patches on boost which I'd love to avoid.

---
