# #1006 - websocket/stream.hpp with spirit/include/qi.hpp [Closed]

> Username: rberlich  
> Created at: 2018-02-04 20:22:52 UTC  
> Updated at: 2018-02-27 16:40:57 UTC  
> Closed at: 2018-02-27 16:40:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1006  

Hi there,  
  
I seem to be running into [this](https://github.com/boostorg/spirit/issues/348) problem when using [Qi](http://www.boost.org/doc/libs/1_66_0/libs/spirit/doc/html/spirit/qi.html) together with Beast. A simple way to reproduce this is to uncomment the qi-header in [async_websocket_server.hpp](https://github.com/rberlich/Estray/blob/master/async_websocket_server.hpp) in [Estray](https://github.com/rberlich/Estray).  
  
Compiling this on MacOS with Clang will result in an error   
  
```  
/opt/boost/include/boost/endian/buffers.hpp:54:12: warning: 'BOOST_ENDIAN_DEFAULT_CONSTRUCT' macro redefined [-Wmacro-redefined]  
#   define BOOST_ENDIAN_DEFAULT_CONSTRUCT = default;  // C++0x  
           ^  
/opt/boost/include/boost/spirit/home/support/detail/endian/endian.hpp:55:10: note: previous definition is here  
# define BOOST_ENDIAN_DEFAULT_CONSTRUCT {}          // C++03   
```  
  
with a similar error on gcc7 / Ubuntu 17.10 . So this seems to be caused by a duplicate definition.  
  
Since I am using qi in a few places which cannot easily be avoided -- is there some other work-around without having to wait for Boost 1.67 or patching 1.66 ?  
  
I am asking this here since this also affects Beast.  
  
Thanks and Kind Regards,  
Beet

---

## Comment 1

> Username: rberlich  
> Created at: 2018-02-05 10:57:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1006#issuecomment-363050797  

A minimal reproducer looks like this (using clang on MacOS):  
```  
#include <boost/spirit/include/qi.hpp>  
#include <boost/beast/websocket.hpp>  
int main(int argc, char **argv) {}  
```  
  
The warning (see the last post) can be avoided like this (hack, and doesn't bring me much further ...):  
  
```  
#include <boost/spirit/include/qi.hpp>  
#include <boost/version.hpp>  
#if BOOST_VERSION == 106600  
#define BOOST_ENDIAN_DEFAULT_CONSTRUCT_SAVE BOOST_ENDIAN_DEFAULT_CONSTRUCT  
#undef BOOST_ENDIAN_DEFAULT_CONSTRUCT  
#include <boost/beast/websocket.hpp>  
#define BOOST_ENDIAN_DEFAULT_CONSTRUCT BOOST_ENDIAN_DEFAULT_CONSTRUCT_SAVE  
#undef BOOST_ENDIAN_DEFAULT_CONSTRUCT_SAVE  
#else  
#include <boost/beast/websocket.hpp>  
#endif /* BOOST_VERSION */  
int main(int argc, char **argv) {}  
```  
  
This still leaves me with the following error:  
  
```  
In file included from [...]  
In file included from /opt/boost/include/boost/beast/websocket.hpp:18:  
In file included from /opt/boost/include/boost/beast/websocket/stream.hpp:3455:  
/opt/boost/include/boost/beast/websocket/impl/stream.ipp:460:28: error: no matching constructor for initialization of 'big_uint16_buf_t' (aka 'endian_buffer<order::big, unsigned short, 16>')  
            ::new(&tmp[0]) big_uint16_buf_t{  
                           ^               ~  
/opt/boost/include/boost/endian/buffers.hpp:341:11: note: candidate constructor (the implicit copy constructor) not viable: no known conversion from 'std::uint16_t' (aka 'unsigned short') to 'const boost::endian::endian_buffer<boost::endian::order::big, unsigned short, 16, boost::endian::align::no>' for 1st argument  
    class endian_buffer< order::big, T, n_bits, align::no >  
          ^  
/opt/boost/include/boost/endian/buffers.hpp:341:11: note: candidate constructor (the implicit move constructor) not viable: no known conversion from 'std::uint16_t' (aka 'unsigned short') to 'boost::endian::endian_buffer<boost::endian::order::big, unsigned short, 16, boost::endian::align::no>' for 1st argument  
/opt/boost/include/boost/endian/buffers.hpp:341:11: note: candidate constructor (the implicit default constructor) not viable: requires 0 arguments, but 1 was provided  
```  
  
Any idea for a work around would be appreciated!  
  
Thanks and Kind Regards,  
Beet

---

## Comment 2

> Username: rberlich  
> Created at: 2018-02-05 12:42:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1006#issuecomment-363073680  

So, as a work-around, I am now getting it to compile by avoiding the qi "catch-all" header and just using the specific "sub-headers" needed by my application.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-02-27 16:40:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1006#issuecomment-368942353  

It looks like this has been resolved and there's nothing for me to do so I'll go ahead and close it, feel free to open a new one if you have another problem!
