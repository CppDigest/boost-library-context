# #75 - try_capture_all assert fails on catching an exception [Closed]

> Username: DirtYiCE  
> Created at: 2024-08-19 00:37:47 UTC  
> Updated at: 2024-08-19 18:12:49 UTC  
> Closed at: 2024-08-19 18:12:49 UTC  
> Url: https://github.com/boostorg/leaf/issues/75  

The documentation for `try_capture_all` says "catching and capturing all exceptions and all communicated error objects in the returned leaf::result object", but instead in my case it just assert fails. Simple example:  
  
```c++  
#include <boost/leaf.hpp>   
  
int main() {  
  boost::leaf::try_capture_all([]() { throw std::runtime_error("x"); });  
}  
```  
  
Fails with:  
```  
a.out: /usr/include/boost/leaf/detail/optional.hpp:158: T &boost::leaf::leaf_detail::optional<boost::leaf::leaf_detail::dynamic_allocator>::value(int) & [T = boost::leaf::leaf_detail::dynamic_allocator]: Assertion `has_value(key) != 0' failed.  
Aborted  
```  
  
If I compile with -DNDEBUG, it simply SIGSEGVs... Tried with system boost 1.85 and the leaf.hpp downloadable from here, same results: https://raw.githubusercontent.com/boostorg/leaf/gh-pages/leaf.hpp

---

## Comment 1

> Username: zajo  
> Created at: 2024-08-19 03:04:07 UTC  
> Url: https://github.com/boostorg/leaf/issues/75#issuecomment-2295578564  

Confirming that this is a bug.

---

## Comment 2

> Username: zajo  
> Created at: 2024-08-19 03:25:23 UTC  
> Url: https://github.com/boostorg/leaf/issues/75#issuecomment-2295594936  

Fix is in feature/issue_75, I will merge in develop and master as soon as GHA CI passes.

---

## Comment 3

> Username: zajo  
> Created at: 2024-08-19 18:12:49 UTC  
> Url: https://github.com/boostorg/leaf/issues/75#issuecomment-2297152738  

Merged in master. Thanks for reporting.
