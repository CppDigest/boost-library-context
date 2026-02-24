# #582 - async_write / file_body / win32 [Closed]

> Username: vinniefalco  
> Created at: 2017-07-03 23:48:56 UTC  
> Updated at: 2017-07-12 19:10:54 UTC  
> Closed at: 2017-07-12 19:10:54 UTC  
> Url: https://github.com/boostorg/beast/issues/582  

This is **in scope**!  
```  
// Uses the TransmitFile Windows API to take advantage of platform optimizations  
//  
template<  
    class Protocol,  
    bool isRequest,  
    class Fields,  
    class CompletionToken>  
async_return_type<CompletionToken, void(error_code)>  
async_write(  
    boost::asio::basic_stream_socket<Protocol>& stream,  
    serializer<isRequest, file_body_win32, Fields> const& sr,  
    CompletionToken&& token);  
  
// similar overload for file_body_posix, and sync versions of both  
```  
  
See:  
https://github.com/chriskohlhoff/asio/blob/master/asio/src/examples/cpp03/windows/transmit_file.cpp#L25  
  
The implementation may need `bool serializer::is_body_started()` to know if it is safe to proceed with `TransmitFile`.  
  
We might consider `async_write_some` instead it could be a natural fit.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-04 01:04:58 UTC  
> Updated at: 2017-07-04 04:07:07 UTC  
> Url: https://github.com/boostorg/beast/issues/582#issuecomment-312757044  

`file_body` will need to be a public interface, this is how it could play out:  
  
```  
// file_body.hpp  
  
#ifdef POSIX  
# include <beast/http/file_body_posix.hpp>  
#elif defined(_MSC_VER)  
# include <beast/http/file_body_win32.hpp>  
#else  
# include <beast/http/file_body_stdc.hpp>  
#endif  
  
namespace beast {  
namespace http {  
  
#ifdef POSIX  
using file_body = file_body_posix; // implementation from nudb::posix_file  
  
#elif defined(_MSC_VER)  
using file_body = file_body_win32; // implementation from nudb::win32_file  
  
#else  
using file_body = file_body_stdc; // the current version of file_body that uses FILE*  
  
#endif  
  
} // http  
} // beast  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-07-04 03:52:06 UTC  
> Updated at: 2017-07-04 03:52:14 UTC  
> Url: https://github.com/boostorg/beast/issues/582#issuecomment-312774173  

> The async_write specialisation needs to also include basic_stream_socket<> as part of its signature, since I believe the optimisation is only available from file to socket.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-04 04:20:18 UTC  
> Url: https://github.com/boostorg/beast/issues/582#issuecomment-312776963  

Platform file bodies like `file_body_win32` will need to still meet the requirements of **BodyWriter** for the case where either we are not using a basic stream socket or if the body gets wrapped.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-07-12 19:10:54 UTC  
> Url: https://github.com/boostorg/beast/issues/582#issuecomment-314868098  

Merged
