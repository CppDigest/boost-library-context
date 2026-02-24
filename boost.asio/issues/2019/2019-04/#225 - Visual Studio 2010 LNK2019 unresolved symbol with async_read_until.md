# #225 - Visual Studio 2010 LNK2019 unresolved symbol with async_read_until [Closed]

> Username: SpareSimian  
> Created at: 2019-04-14 19:47:40 UTC  
> Updated at: 2020-12-30 01:03:07 UTC  
> Closed at: 2020-12-30 01:03:06 UTC  
> Url: https://github.com/boostorg/asio/issues/225  

The following test code (inside #if 1) generates a LNK2019 (unresolved external symbol) with 1.70.0 but links successfully with 1.69.0. Inspecting read_until.hpp, I suspect the problem is with the enable_if expansion being too complex for the older compiler. (Hence the #if0 code which tests the individual conditions, but those seem to work fine.) The same code links with VS2019 and VS2015, and this linked under the previous Boost with VS2010.  
  
```  
int _tmain(int argc, _TCHAR* argv[])  
{  
   typedef boost::asio::streambuf buftype;  
#if 0  
   const bool v1 = boost::asio::is_dynamic_buffer_v1<typename boost::decay<buftype>::type>::value;  
   const bool notv2 = !boost::asio::is_dynamic_buffer_v2<typename boost::decay<buftype>::type>::value;  
#endif  
#if 0  
   void* cond = boost::enable_if<  
                boost::asio::is_dynamic_buffer_v1<typename boost::decay<buftype>::type>::value  
                && !boost::asio::is_dynamic_buffer_v2<typename boost::decay<buftype>::type>::value  
                >::type* 0;  
#endif  
#if 1  
   boost::asio::io_service ios;  
   boost::asio::ip::tcp::socket socket(ios);  
   boost::asio::streambuf buf;  
   boost::asio::async_read_until(socket, buf, '\n', NULL);  
#endif  
   return 0;  
}  
```

---

## Comment 1

> Username: SpareSimian  
> Created at: 2019-04-14 19:56:29 UTC  
> Url: https://github.com/boostorg/asio/issues/225#issuecomment-483051355  

I just checked and the problem code also links with VS2013.

---

## Comment 2

> Username: SpareSimian  
> Created at: 2019-04-17 20:37:42 UTC  
> Url: https://github.com/boostorg/asio/issues/225#issuecomment-484251870  

On a hunch I forced expansion of the secondary template and now see that it's failing because it's trying to copy a streambuf.  
  
```  
#include "stdafx.h"  
#include <boost/asio.hpp>  
  
void handleRead(const boost::system::error_code& ec, std::size_t size) {}  
  
int _tmain(int argc, _TCHAR* argv[])  
{  
   boost::asio::io_service ios;  
   boost::asio::ip::tcp::socket socket(ios);  
   boost::asio::streambuf buf;  
   boost::asio::async_read_until(socket, buf, '\n', handleRead, NULL);  
   return 0;  
}  
```  
Error message:  
```  
f:\devel\boost\boost_1_70_0\boost\asio\basic_streambuf.hpp(361): error C2248: 'boost::asio::detail::noncopyable::noncopyable' : cannot access private member declared in class 'boost::asio::detail::noncopyable'  
          f:\devel\boost\boost_1_70_0\boost\asio\detail\noncopyable.hpp(32) : see declaration of 'boost::asio::detail::noncopyable::noncopyable'  
          f:\devel\boost\boost_1_70_0\boost\asio\detail\noncopyable.hpp(27) : see declaration of 'boost::asio::detail::noncopyable'  
          This diagnostic occurred in the compiler generated function 'boost::asio::basic_streambuf<>::basic_streambuf(const boost::asio::basic_streambuf<> &)'  
  
```

---

## Comment 3

> Username: SpareSimian  
> Created at: 2019-04-17 21:24:16 UTC  
> Url: https://github.com/boostorg/asio/issues/225#issuecomment-484267139  

To get the above, I also had to comment out the declaration in boost/asio/read_until.hpp at line 1607 as VS 2010 thinks it's ambiguous (error C2668) with the definition of the same template at boost/asio/impl/read_until.hpp line 1021!

---

## Comment 4

> Username: ghost  
> Created at: 2020-12-30 01:03:05 UTC  
> Url: https://github.com/boostorg/asio/issues/225#issuecomment-752291859  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#683](https://github.com/chriskohlhoff/asio/issues/683).
