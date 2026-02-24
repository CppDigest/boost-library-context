# #77 - detail/consuming_buffers.hpp triggers a g++-5 parsing error [Closed]

> Username: cotequeiroz  
> Created at: 2018-01-11 11:09:51 UTC  
> Updated at: 2020-12-30 00:47:23 UTC  
> Closed at: 2020-12-30 00:47:11 UTC  
> Url: https://github.com/boostorg/asio/issues/77  

While trying to compile Domoticz, I ran into a strange parsing error with g++5 in `detail/consuming_buffers.hpp`.   
```/usr/include/boost/asio/detail/consuming_buffers.hpp:105:50: error: parse error in template argument list  
     while (next != end && max_size > 0 && result.count < result.max_buffers)  
                                                  ^  
```  
The code compiles fine with g++6.  
I'm no c++ programmer, so excuse me if my explanation is not accurate, but apparently g++ thinks this is a template invocation and chokes.   
Even though this seems to be a g++ error, adding parenthesis around `result.count` will allow compilation to proceed.  
```  
--- a/boost/asio/detail/consuming_buffers.hpp  
+++ b/boost/asio/detail/consuming_buffers.hpp  
@@ -102,7 +102,7 @@ public:  
  
     std::advance(next, next_elem_);  
     std::size_t elem_offset = next_elem_offset_;  
-    while (next != end && max_size > 0 && result.count < result.max_buffers)  
+    while (next != end && max_size > 0 && (result.count) < result.max_buffers)  
     {  
       Buffer next_buf = Buffer(*next) + elem_offset;  
       result.elems[result.count] = boost::asio::buffer(next_buf, max_size);  
```

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:46:59 UTC  
> Url: https://github.com/boostorg/asio/issues/77#issuecomment-752288630  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#594](https://github.com/chriskohlhoff/asio/issues/594).
