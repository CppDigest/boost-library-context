# #417 - BOOST_ASIO_HAS_STD_FUTURE_CLASS detection bug on clang-cl [Open]

> Username: mark-99  
> Created at: 2023-04-13 16:53:48 UTC  
> Updated at: 2023-04-13 16:53:48 UTC  
> Url: https://github.com/boostorg/asio/issues/417  

clang-cl on Windows uses the clang compiler with the MSVC STL. In the detection logic here https://github.com/boostorg/asio/blob/develop/include/boost/asio/detail/future.hpp  
  
`__GNUC__` is true (as clang sets it)  
`BOOST_ASIO_HAS_CLANG_LIBCXX` is false (as it's MSVC STL)  
`_GLIBCXX_HAS_GTHREADS` is false (as it's not that either)  
So this leaves `BOOST_ASIO_HAS_STD_FUTURE_CLASS` undefined.  
  
This in turn causes `#include <boost/asio/packaged_task.hpp>` to silently fail to define anything, and that leads to an obscure error message about incomplete types and unexpected void when trying to e.g. post a packaged_task (ie the same error message as if you try to use that functionality without including that header, as now the header is effectively empty).  
  
Workaround is to manually define `BOOST_ASIO_HAS_STD_FUTURE_CLASS`.
