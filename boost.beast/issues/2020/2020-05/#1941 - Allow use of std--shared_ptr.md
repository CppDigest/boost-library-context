# #1941 - Allow use of std::shared_ptr [Closed]

> Username: Anton-V-K  
> Created at: 2020-05-04 16:54:20 UTC  
> Updated at: 2020-05-05 19:12:40 UTC  
> Closed at: 2020-05-05 19:12:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1941  

The library should allow usage of `std::shared_ptr` instead of `boost::shared_ptr` in a way like it is done for `asio` (you can define `BOOST_ASIO_HAS_STD_SHARED_PTR` to switch to `std::shared_ptr`).  
The files which have `#include <boost/shared_ptr.hpp>` are:  
  
- `.../beast/websocket/stream.hpp`  
- `.../beast/core/basic_stream.hpp`  
- `.../beast/_experimental/test/stream.hpp`  
- `.../beast/websocket/impl/stream_impl.hpp`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-05-04 16:56:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1941#issuecomment-623581831  

The library already "allows" you to use `std::shared_ptr`. Nothing stops you from using `std::shared_ptr` with Beast in your program.

---

## Comment 2

> Username: Anton-V-K  
> Created at: 2020-05-04 17:46:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1941#issuecomment-623607784  

@vinniefalco , you're right.  
The issue is mainly for the cases when `boost::shared_ptr` isn't available for compiling `beast`.  
Particularly I ran into this issue when trying to import `beast` into the code base, which doesn't use boost-pointers.  
Moreover after examining the library code I see that use of `shared_ptr` isn't consistent - in some cases it is from STL, in other cases it is from BOOST.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-05-04 17:50:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1941#issuecomment-623609643  

> The issue is mainly for the cases when boost::shared_ptr isn't available for compiling beast  
  
But that's impossible. Beast requires Boost, and Boost has `boost::shared_ptr`. So it will always be available.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-05-04 17:51:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1941#issuecomment-623610318  

>  I see that use of shared_ptr isn't consistent  
  
Are you talking about `chunk_encode`? I suppose that could be fixed.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-05-04 17:53:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1941#issuecomment-623611067  

Also note that `std::enable_shared_from_this::weak_from_this` is C++17. Beast uses Boost shared pointers because they support `weak_from_this` in C++11. That is why you see the Boost version being used, and also why sometimes the library uses the std version.
