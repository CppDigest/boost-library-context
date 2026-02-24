# #2936 - basic_stream's (websocket::stream) async_connect compilation (type not convertible) failure introduced between Boost 1.85.0 and Boost 1.86.0 [Closed]

> Username: 13steinj  
> Created at: 2024-10-05 03:04:35 UTC  
> Updated at: 2024-10-29 17:17:58 UTC  
> Closed at: 2024-10-29 17:17:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2936  

### Version of Beast  
  
Boost 1.86.0  
  
### Steps necessary to reproduce the problem  
  
Sample code and [godbolt](https://gcc.godbolt.org/z/eMaz79Eb5) (yes I know this minimum example is contrived, but I don't ~~want to share awful code~~ know what's intellectual property and what isn't in this section of the codebase).  
  
```cpp  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
  
namespace bb = boost::beast;  
  
extern boost::beast::websocket::stream<boost::beast::tcp_stream>* stream_;  
extern void onConnect(boost::beast::error_code ec, boost::asio::ip::tcp::resolver::results_type::endpoint_type);  
  
void onResolve(boost::beast::error_code ec, boost::asio::ip::tcp::resolver::results_type results) {  
    boost::beast::get_lowest_layer(*stream_).async_connect(  
        results, [](auto ec, auto endpoint) { onConnect(ec, endpoint); });  
}  
```  
  
Simple "solution" is to change `auto endpoint` to `boost::asio::ip::tcp::resolver::results_type::endpoint_type`... but I have absolutely no idea if this changes function generation / execution compared to what's intended; it appears as though this is a mixed Asio/Beast bug. Reduced to a few commits in Boost Asio / Boost Beast, specifically:  
  
* Boost Beast @ Boost 1.85 and Boost Asio @ Boost 1.85 are fine together.  
* Boost Asio introduced https://github.com/boostorg/asio/commit/6ce241c2bb4217c1f22f0b25f70af637dcf27e37 which caused (can also cause?) #2867  
* https://github.com/boostorg/beast/commit/1a2b85b7a4a5edd6ab5f3d84bdaa50e3b09bc471 was introduced via #2893 to fix #2867  
  
Asio had no release notes available in the Boost release, I suspect this is a case of "Asio changed a default, Beast changed [to fix ambiguity in #2867 via #2893] but introduced a different behavior change by accident."  
  
### All relevant compiler information  
  
I can't think of anything that is particularly relevant, but GCC 13.3 (or 14.2); `-std=c++26` is enough to trigger this.

---

## Comment 1

> Username: ashtum  
> Created at: 2024-10-05 07:12:54 UTC  
> Updated at: 2024-10-05 08:29:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2936#issuecomment-2394960365  

This appears to be an issue in Asio, as the following code fails to compile:  
  
```C++  
boost::asio::async_connect(  
    boost::beast::get_lowest_layer(*stream_).socket(),  
    results,  
    [](auto ec, auto endpoint) { onConnect(ec, endpoint); });  
```  
  
A workaround is to add a trailing return type to the lambda, which ensures that the `is_connect_condition` trait works correctly:  
  
```C++  
boost::asio::async_connect(  
    boost::beast::get_lowest_layer(*stream_).socket(),  
    results,  
    [](auto ec, auto endpoint) -> void { onConnect(ec, endpoint); });  
```  
  
<s>I recommend opening an issue in Asio so that this can be addressed.</s>  
it seems the issue is that the generic lambda passed to `async_connect` made it SFINAE-unfriendly. You can either use a trailing return type or a concrete type for the `endpoint` argument.

---

## Comment 2

> Username: 13steinj  
> Created at: 2024-10-07 16:13:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2936#issuecomment-2397355814  

> ~~I recommend opening an issue in Asio so that this can be addressed.~~  
it seems the issue is that the generic lambda passed to async_connect made it SFINAE-unfriendly. You can either use a trailing return type or a concrete type for the endpoint argument.  
  
Sorry, I don't fully follow-- I mean, yes, but the example you provided that fails to compile fails in both 1.85 and 1.86. The example I provided only fails in 1.86, they appear to be related examples but the semantics are different? The notable thing is the lambda's template `operator()` gets instantiated with _at least_ the endpoint type and with the `...resolver_iterator...` type.  
  
The odder thing is... I'm a bit surprised that explicitly specifying the return type is another valid workaround.

---

## Comment 3

> Username: ashtum  
> Created at: 2024-10-07 16:30:11 UTC  
> Updated at: 2024-10-07 16:53:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2936#issuecomment-2397389861  

> The odder thing is... I'm a bit surprised that explicitly specifying the return type is another valid workaround.  
  
Chris added the `is_connect_condition` type trait to resolve ambiguity in several overloads of `async_connect`. The generic lambda you passed to `async_connect` made it SFINAE-unfriendly, causing the evaluation of `is_connect_condition` to result in a compilation error. By adding a trailing return type or using a concrete type for the second parameter, the type trait can detect that the lambda is not a `connect_condition` earlier without the instantiation of the lambda's call operator.
