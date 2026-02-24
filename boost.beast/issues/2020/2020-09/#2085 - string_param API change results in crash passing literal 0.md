# #2085 - string_param API change results in crash passing literal 0 [Closed]

> Username: ecatmur  
> Created at: 2020-09-10 19:46:05 UTC  
> Updated at: 2020-09-14 06:58:32 UTC  
> Closed at: 2020-09-14 06:58:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2085  

Since #1956 1.74.0:  
```  
#include <boost/beast.hpp>  
int main() {  
    boost::beast::http::response<boost::beast::http::string_body> res;  
    res.set(boost::beast::http::field::content_length, 0);  
}  
```  
  
Expected: fails to compile  
Actual: compiles, crashes with segmentation fault (null dereference).  
  
https://godbolt.org/z/7G1qqv (middle pane is Boost 1.73, right pane is Boost 1.74).  
  
For us, this only broke a test, but other people might be passing literal 0 as a field value in production code. I'd suggest adding  
```  
void ${function-name}(field name, std::nullptr_t) = delete;  
```  
alongside each place `string_param` was changed to `string_view` in 14c3d50b570b1d7bf32453f38be77c759007fdb0.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-09-10 19:48:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2085#issuecomment-690679915  

The road to Hell is paved with good intentions. Thanks for letting us know.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-10 19:57:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2085#issuecomment-690684566  

> void ${function-name}(field name, std::nullptr_t) = delete;  
Hmm... good idea.
