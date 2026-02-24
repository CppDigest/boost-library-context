# #808 - Compilation errors when including zlib.h [Closed]

> Username: kylelutz  
> Created at: 2017-10-06 17:14:39 UTC  
> Updated at: 2017-10-11 18:25:26 UTC  
> Closed at: 2017-10-11 17:58:26 UTC  
> Url: https://github.com/boostorg/beast/issues/808  

I'm working on integrating Boost.Beast (at 0bc2a414043f4a9430e71e6664d592e67ea6d282) into a larger project, but currently having issues when compiling code which includes both zlib and beast headers.  
  
Simple repro is:  
  
```  
#include <zlib.h>  
#include <boost/beast/websocket.hpp>  
  
int main() { return 0; }  
```  
  
which fails when compiling with:  
  
```  
$ clang++-3.8 -I/code/beast/include -std=c++11 beast_zlib_bug.cpp                                                                                                                                                                                                                                                                                      
In file included from beast_zlib_bug.cpp:2:  
In file included from /code/beast/include/boost/beast/websocket.hpp:18:  
In file included from /code/beast/include/boost/beast/websocket/stream.hpp:22:  
In file included from /code/beast/include/boost/beast/websocket/detail/pmd_extension.hpp:16:  
In file included from /code/beast/include/boost/beast/zlib/deflate_stream.hpp:15:  
/code/beast/include/boost/beast/zlib/zlib.hpp:57:5: error: expected identifier  
    Z_BINARY    = 0,  
    ^  
/usr/include/zlib.h:199:20: note: expanded from macro 'Z_BINARY'  
#define Z_BINARY   0  
                   ^  
In file included from beast_zlib_bug.cpp:2:  
In file included from /code/beast/include/boost/beast/websocket.hpp:18:  
In file included from /code/beast/include/boost/beast/websocket/stream.hpp:22:  
In file included from /code/beast/include/boost/beast/websocket/detail/pmd_extension.hpp:16:  
In file included from /code/beast/include/boost/beast/zlib/deflate_stream.hpp:15:  
/code/beast/include/boost/beast/zlib/zlib.hpp:58:5: error: expected identifier  
    Z_TEXT      = 1,  
    ^  
/usr/include/zlib.h:200:20: note: expanded from macro 'Z_TEXT'  
#define Z_TEXT     1  
                   ^  
In file included from beast_zlib_bug.cpp:2:  
In file included from /code/beast/include/boost/beast/websocket.hpp:18:  
In file included from /code/beast/include/boost/beast/websocket/stream.hpp:22:  
In file included from /code/beast/include/boost/beast/websocket/detail/pmd_extension.hpp:16:  
In file included from /code/beast/include/boost/beast/zlib/deflate_stream.hpp:15:  
/code/beast/include/boost/beast/zlib/zlib.hpp:59:5: error: expected identifier  
    Z_UNKNOWN   = 2  
    ^  
/usr/include/zlib.h:202:20: note: expanded from macro 'Z_UNKNOWN'  
#define Z_UNKNOWN  2  
                   ^  
In file included from beast_zlib_bug.cpp:2:  
In file included from /code/beast/include/boost/beast/websocket.hpp:18:  
In file included from /code/beast/include/boost/beast/websocket/stream.hpp:22:  
In file included from /code/beast/include/boost/beast/websocket/detail/pmd_extension.hpp:16:  
In file included from /code/beast/include/boost/beast/zlib/deflate_stream.hpp:15:  
/code/beast/include/boost/beast/zlib/zlib.hpp:130:5: error: expected identifier  
    Z_NO_COMPRESSION        =  0,  
    ^  
/usr/include/zlib.h:186:34: note: expanded from macro 'Z_NO_COMPRESSION'  
#define Z_NO_COMPRESSION         0  
                                 ^  
In file included from beast_zlib_bug.cpp:2:  
In file included from /code/beast/include/boost/beast/websocket.hpp:18:  
In file included from /code/beast/include/boost/beast/websocket/stream.hpp:22:  
In file included from /code/beast/include/boost/beast/websocket/detail/pmd_extension.hpp:16:  
In file included from /code/beast/include/boost/beast/zlib/deflate_stream.hpp:15:  
/code/beast/include/boost/beast/zlib/zlib.hpp:131:5: error: expected identifier  
    Z_BEST_SPEED            =  1,  
    ^  
/usr/include/zlib.h:187:34: note: expanded from macro 'Z_BEST_SPEED'  
#define Z_BEST_SPEED             1  
                                 ^  
In file included from beast_zlib_bug.cpp:2:  
In file included from /code/beast/include/boost/beast/websocket.hpp:18:  
In file included from /code/beast/include/boost/beast/websocket/stream.hpp:22:  
In file included from /code/beast/include/boost/beast/websocket/detail/pmd_extension.hpp:16:  
In file included from /code/beast/include/boost/beast/zlib/deflate_stream.hpp:15:  
/code/beast/include/boost/beast/zlib/zlib.hpp:132:5: error: expected identifier  
    Z_BEST_COMPRESSION      =  9,  
    ^  
/usr/include/zlib.h:188:34: note: expanded from macro 'Z_BEST_COMPRESSION'  
#define Z_BEST_COMPRESSION       9  
                                 ^  
In file included from beast_zlib_bug.cpp:2:  
In file included from /code/beast/include/boost/beast/websocket.hpp:18:  
In file included from /code/beast/include/boost/beast/websocket/stream.hpp:22:  
In file included from /code/beast/include/boost/beast/websocket/detail/pmd_extension.hpp:16:  
In file included from /code/beast/include/boost/beast/zlib/deflate_stream.hpp:15:  
/code/beast/include/boost/beast/zlib/zlib.hpp:133:5: error: expected identifier  
    Z_DEFAULT_COMPRESSION   = -1  
    ^  
/usr/include/zlib.h:189:32: note: expanded from macro 'Z_DEFAULT_COMPRESSION'  
#define Z_DEFAULT_COMPRESSION  (-1)  
                               ^  
In file included from beast_zlib_bug.cpp:2:  
In file included from /code/beast/include/boost/beast/websocket.hpp:18:  
In file included from /code/beast/include/boost/beast/websocket/stream.hpp:22:  
In file included from /code/beast/include/boost/beast/websocket/detail/pmd_extension.hpp:16:  
In file included from /code/beast/include/boost/beast/zlib/deflate_stream.hpp:16:  
/code/beast/include/boost/beast/zlib/detail/deflate_stream.hpp:161:35: error: expected member name or ';' after declaration specifiers  
    static std::uint8_t constexpr MAX_MEM_LEVEL = 9;  
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ ^  
/usr/include/zconf.h:237:27: note: expanded from macro 'MAX_MEM_LEVEL'  
#    define MAX_MEM_LEVEL 9  
                          ^  
8 errors generated.  
  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-10-06 17:21:02 UTC  
> Url: https://github.com/boostorg/beast/issues/808#issuecomment-334817568  

It looks like I need to rename the types in `<boost/beast/zlib/zlib.hpp>` thanks!

---

## Comment 2

> Username: kylelutz  
> Created at: 2017-10-11 18:25:25 UTC  
> Url: https://github.com/boostorg/beast/issues/808#issuecomment-335903473  

Thanks for the fix!
