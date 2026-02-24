# #2939 - `static` global functions in headers interferes with use of modules [Closed]

> Username: davidstone  
> Created at: 2024-10-10 00:55:41 UTC  
> Updated at: 2024-10-17 13:53:09 UTC  
> Closed at: 2024-10-17 13:53:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2939  

Beast has some global functions in its headers (for instance, `clamp` in `boost/beast/core/detail/clamp.hpp`) that are declared `static`. `static` global functions are inaccessible to the second phase of name lookup in templates instantiated outside of the module. So for instance, if I have a module like  
  
```c++  
module;  
  
#include <boost/beast.hpp>  
#include <string_view>  
  
export module thingy;  
  
struct Thingy {  
    void f(std::string_view text, auto function) {  
        m_socket.async_write(boost::asio::buffer(text), function);  
    }  
private:  
    boost::beast::websocket::stream<boost::asio::ip::tcp::socket> m_socket;  
};  
```  
  
If a user attempts to call `Thingy::f`, they will get an error message like  
  
```console  
boost/beast/websocket/impl/write.hpp:238:35: fatal error: no matching function for call to 'clamp'  
  238 |             bytes_transferred_ += clamp(fh_.len);  
```  
  
This is because the `clamp` overloads are declared `static` (in the "translation-unit local" sense).  
  
Removing `static` would fix this problem. `clamp` is a set of function templates so it doesn't need any other change. I'm not sure if there are other functions that have a similar issue, but that's the only one I've run into so far.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2024-10-10 00:56:46 UTC  
> Updated at: 2024-10-10 00:57:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2939#issuecomment-2403687823  

LOL should have just submitted a pull request. This looks like a typo and thank you for finding it :)
