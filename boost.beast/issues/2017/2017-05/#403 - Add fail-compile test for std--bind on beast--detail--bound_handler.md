# #403 - Add fail-compile test for std::bind on beast::detail::bound_handler [Closed]

> Username: vinniefalco  
> Created at: 2017-05-31 17:22:37 UTC  
> Updated at: 2018-04-27 03:00:59 UTC  
> Closed at: 2018-04-27 03:00:59 UTC  
> Url: https://github.com/boostorg/beast/issues/403  

Although not practical with CMake (because of Visual Studio) the bjam build could have a pass-fail-compile test for the deleted std::bind in `<beast/core/bind_handler.hpp>`

---

## Comment 1

> Username: stale[bot]  
> Created at: 2018-04-24 17:52:42 UTC  
> Url: https://github.com/boostorg/beast/issues/403#issuecomment-384022663  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-04-27 03:00:59 UTC  
> Url: https://github.com/boostorg/beast/issues/403#issuecomment-384849698  

This offers too little in return for a lot of work which messes with the build scripts.
