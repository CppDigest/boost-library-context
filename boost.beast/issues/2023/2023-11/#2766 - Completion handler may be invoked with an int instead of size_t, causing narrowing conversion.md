# #2766 - Completion handler may be invoked with an int instead of size_t, causing narrowing conversion [Closed]

> Username: fabiorossetto  
> Created at: 2023-11-14 15:48:40 UTC  
> Updated at: 2023-11-22 06:12:42 UTC  
> Closed at: 2023-11-15 08:39:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2766  

### Version of Beast  
1.83.0  
  
### Problem description  
In `impl/basic_stream.hpp` we find the following code:  
  
```c++  
        this->set_allowed_cancellation(net::cancellation_type::all);  
        if (buffer_bytes(b_) == 0 && state().pending)  
        {  
            this->complete(false, error_code(), 0);  
        }  
        else  
        {  
            pg_.assign(state().pending);  
            (*this)({});  
        }  
```  
  
Passing "0" as size will cause the completion handler to be invoked with an `int` for the size. This can lead to narrowing conversions.  
  
Simplest example:  
```c++  
#include <boost/beast/core/tcp_stream.hpp>  
  
using namespace boost::beast;  
  
struct Outcome {  
   boost::system::error_code code;  
   size_t size;  
};  
  
int main() {  
    boost::asio::io_context context;  
    tcp_stream stream{context};  
    std::vector<char> buffer;  
    stream.async_read_some(boost::asio::buffer(buffer), []<typename... Args>(Args&&... args){  
        Outcome{std::forward<Args>(args)...};  
    });  
}  
```  
  
With `-Wall -Werror`, the code does not compile.

---

## Comment 1

> Username: ashtum  
> Created at: 2023-11-14 17:08:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2766#issuecomment-1810711696  

@fabiorossetto That's correct; the second argument should have an explicit type of `std::size_t`. I believe we would be able to fix it within the time window for bug fixes in Boost 1.84.

---

## Comment 2

> Username: fabiorossetto  
> Created at: 2023-11-16 13:48:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2766#issuecomment-1814469054  

Wow that was fast! Thank you!

---

## Comment 3

> Username: ashtum  
> Created at: 2023-11-22 06:12:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2766#issuecomment-1822172124  

The fix has been merged into the master branch and will be included in the Boost 1.84 release.  
https://github.com/boostorg/beast/blob/d0dd9c50694bcbb8836775e780eb046f66b3e1d7/include/boost/beast/core/impl/basic_stream.hpp#L300
