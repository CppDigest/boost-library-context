# #15 - boost::exception should be nothrow-copy-constructible [Closed]

> Username: tonyelewis  
> Created at: 2018-11-14 16:48:33 UTC  
> Updated at: 2019-06-07 19:43:04 UTC  
> Closed at: 2019-06-07 19:41:41 UTC  
> Url: https://github.com/boostorg/exception/issues/15  

`clang-tidy`'s [`cert-err60-cpp`](https://clang.llvm.org/extra/clang-tidy/checks/cert-err60-cpp.html) check has alerted me to CERT Coding Standard rule [ERR60-CPP](https://wiki.sei.cmu.edu/confluence/display/cplusplus/ERR60-CPP.+Exception+objects+must+be+nothrow+copy+constructible) that exception types should be nothrow-copy-constructible.  
  
At present, a `boost::exception` fails this `clang-tidy` check and `std::is_nothrow_copy_constructible_v` agrees&hellip;  
  
~~~cpp  
#include <type_traits>  
  
#include <boost/exception/all.hpp>  
  
static_assert( ::std::is_nothrow_copy_constructible_v< ::boost::exception > );  
~~~  
  
~~~no-highlight  
> clang++ -std=c++17 -stdlib=libc++ -fsyntax-only -isystem include -isystem /opt/include a.cpp  
a.cpp:5:1: error: static_assert failed due to requirement '::std::is_nothrow_copy_constructible_v< ::boost::exception>'  
static_assert( ::std::is_nothrow_copy_constructible_v< ::boost::exception > );  
^              ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
1 error generated.  
~~~  
  
~~~no-highlight  
> g++ -std=c++17 -fsyntax-only -isystem include -isystem /opt/include a.cpp   
a.cpp:5:16: error: static assertion failed  
 static_assert( ::std::is_nothrow_copy_constructible_v< ::boost::exception > );  
~~~  
  
[This comment in the code](https://github.com/boostorg/throw_exception/blob/074bc77efb421388461cfadca751bca7e28ca112/include/boost/exception/exception.hpp#L234) gives the impression that we might reasonably hope `exception` might be nothrow-copy-constructible and that it could be marked as such with some of Boost.Config's `BOOST_NOEXCEPT` macros.  
  
Is this possible?  
  
If a `boost::exception` is sometimes/always not nothrow-copy-constructible, it would be good to highlight this issue in the docs so that people are aware if/when their use of Boost.Exception violates a CERT rule.  
  
Thanks for your work on this library.

---

## Comment 1

> Username: zajo  
> Created at: 2018-11-14 17:04:20 UTC  
> Url: https://github.com/boostorg/exception/issues/15#issuecomment-438738838  

`boost::exception` does not throw on copy. As to `BOOST_NOEXCEPT`, the reason it hasn't been used is that originally, `<boost/exception/exception.hpp>` was extremely light, it did not `#include` any headers, not even standard headers, nevermind `<boost/config.hpp>`.  
  
I see that at some point we did end up including `<boost/config.hpp>`, so this should be an easy change.  
  
By the way, `<boost/exception/exception.hpp>` isn't in this repo.

---

## Comment 2

> Username: tonyelewis  
> Created at: 2018-11-14 18:35:25 UTC  
> Url: https://github.com/boostorg/exception/issues/15#issuecomment-438769950  

Thanks for you reply. That all makes sense. Good news.  
  
Apologies re the repos - the lack of issues on the `throw_exception` repo made me think it might be  treated as a subsidiary of this one. GitHub has a new "Transfer this issue" feature in Beta so you could try transferring this across. Or I'm happy to reopen there and reference this?  
  
Thanks.

---

## Comment 3

> Username: zajo  
> Created at: 2018-11-16 00:06:54 UTC  
> Url: https://github.com/boostorg/exception/issues/15#issuecomment-439235510  

No that's fine, let's leave it here. Logically, `<boost/exception/exception.hpp>` is part of Boost Exception. I'll wait until the current Boost release cycle is over to make this change. Or you can send a pull request.

---

## Comment 4

> Username: zajo  
> Created at: 2019-06-07 19:43:04 UTC  
> Url: https://github.com/boostorg/exception/issues/15#issuecomment-500014665  

bfddc104c6a5701d508a52e62eaed225670e6910
