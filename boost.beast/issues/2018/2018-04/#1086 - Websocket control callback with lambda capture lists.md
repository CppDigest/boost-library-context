# #1086 - Websocket control callback with lambda capture lists [Closed]

> Username: mathijs727  
> Created at: 2018-04-02 20:13:28 UTC  
> Updated at: 2018-04-03 11:37:10 UTC  
> Closed at: 2018-04-03 11:37:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1086  

I ran into a problem with the implementation/documentation of the websocket control_callback:  
https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_websocket/control_frames.html  
  
The callback function passed to control_callback is stored as a std::function but is assigned through std::ref.  
When one tries to pass a lambda with capture list this will not throw a compiler error.  
If the lambda is not stored in a std::function (or the function is not kept alive long enough) then the callback will be called successfully but capture variables will contain arbitrary values.  
This will only cause a crash if you try to dereference a captured pointer variable (such as a "this") making it hard to debug.  
Changing line 603 of  boost/beast/websocket/stream.hpp from `ctrl_cb_ = std::ref(cb);` to `ctrl_cb_ = cb;` fixes the issue (confirming that std::ref is the cause of the problem).  
  
If capture lists are not supported then it should trigger a compile-time error instead of filling captured variables with random values.  
  
It seems like this was an intentional decision (issue #653 ).  
What is the reason for supporting capture lists in async_read/async_write operations but not in control_callback?  
  
### Version of Beast  
Boost 1.66.0  
  
### Problematic code  
```  
int x = 0;  
websocket.control_callback([x](auto frameKind, auto payload) {  
	std::cout << "Captured value: " << x << std::endl;  
});  
websocket.ping("");// Ask the server for a pong message  
```  
  
### All relevant compiler information  
Tested on MSVC 19.13.26129, GCC 7.3.1 and Clang 6.0.0

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-04-02 20:20:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1086#issuecomment-378032308  

The purpose of storing a reference was to avoid a memory allocation, but Peter Dimov pointed out that if the implementation stored a copy, the user could still avoid allocation by calling `control_callback` with an rvalue of type `std::reference_wrapper` (i.e. a return value from `std::ref`). This has been changed in 1.67 to store a copy, see: https://github.com/boostorg/beast/commit/8181851719dd736d7895dec1b0729f22d957d4f4#diff-00c8589c3f6d37eff97e04e458cf1ec4

---

## Comment 2

> Username: mathijs727  
> Created at: 2018-04-03 11:37:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1086#issuecomment-378219666  

Dear Vinnie,  
  
Thank you for the update and the library itself of coarse.  
Hopefully this change will make it into Boost soon.  
  
Mathijs
