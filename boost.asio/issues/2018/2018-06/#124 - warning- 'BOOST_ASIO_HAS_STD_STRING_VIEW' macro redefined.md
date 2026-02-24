# #124 - warning: 'BOOST_ASIO_HAS_STD_STRING_VIEW' macro redefined [Closed]

> Username: hia3  
> Created at: 2018-06-14 22:17:22 UTC  
> Updated at: 2020-12-30 00:52:01 UTC  
> Closed at: 2020-12-30 00:52:00 UTC  
> Url: https://github.com/boostorg/asio/issues/124  

Z:\>type t.cpp  
#include <boost/asio/io_context.hpp>  
int main(){}  
  
Z:\>"clang++" -std=c++17 t.cpp -Iboost  
In file included from t.cpp:1:  
In file included from boost\boost/asio/io_context.hpp:18:  
boost\boost/asio/detail/config.hpp:795:13: warning: 'BOOST_ASIO_HAS_STD_STRING_VIEW' macro redefined [-Wmacro-redefined]  
    #    define BOOST_ASIO_HAS_STD_STRING_VIEW  
                    ^  
boost\boost/asio/detail/config.hpp:782:14: note: previous definition is here  
    #     define BOOST_ASIO_HAS_STD_STRING_VIEW 1  
                     ^

---

## Comment 1

> Username: JVApen  
> Created at: 2018-06-16 14:21:35 UTC  
> Url: https://github.com/boostorg/asio/issues/124#issuecomment-397815531  

I'm encountering the exact same warning on the code base I'm working on, making use of clang-cl on windows.  
Looks like both the clang as the MSVC feature detection indicate that <string_view> exists

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 00:51:59 UTC  
> Url: https://github.com/boostorg/asio/issues/124#issuecomment-752289651  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#625](https://github.com/chriskohlhoff/asio/issues/625).
