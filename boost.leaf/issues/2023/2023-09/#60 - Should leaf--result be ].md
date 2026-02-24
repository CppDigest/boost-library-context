# #60 - Should leaf::result be [[nodiscard]]? [Closed]

> Username: Snarpix  
> Created at: 2023-09-22 11:29:06 UTC  
> Updated at: 2024-07-29 02:55:04 UTC  
> Closed at: 2024-07-29 02:55:04 UTC  
> Url: https://github.com/boostorg/leaf/issues/60  

There is no good use case to silently ignore error...

---

## Comment 1

> Username: zajo  
> Created at: 2023-09-22 23:45:38 UTC  
> Url: https://github.com/boostorg/leaf/issues/60#issuecomment-1732131095  

Indeed. LEAF is (mostly) C++11, it's why this was omitted initially, but you're right, if C++17 is available, `[[nodiscard]]` should be used. Fixed.

---

## Comment 2

> Username: indiosmo  
> Created at: 2024-05-08 20:01:50 UTC  
> Updated at: 2024-05-08 20:02:49 UTC  
> Url: https://github.com/boostorg/leaf/issues/60#issuecomment-2101332115  

Hi, it looks like this commit https://github.com/boostorg/leaf/commit/971ecacf0fb753bf51f892589249df315b7522c6 removed the [[nodiscard]] attribute.  
  
The commit in question entered in 1.85, it was working in 1.84.  
  
The class declaration changed from `class BOOST_LEAF_NODISCARD result` to  `class BOOST_LEAF_SYMBOL_VISIBLE result`.  
  
This can be checked with the following example:  
  
  
```cpp  
#include "boost/leaf.hpp"  
  
// uncomment nodiscard attribute to trigger warninng  
/*[[nodiscard]]*/ boost::leaf::result<void> func() {  
  return {};  
}  
  
int main()  
{  
  func();  
  return 0;  
}  
```

---

## Comment 3

> Username: indiosmo  
> Created at: 2024-05-08 20:08:14 UTC  
> Url: https://github.com/boostorg/leaf/issues/60#issuecomment-2101341497  

Changing the lines below seems to do the trick but I'm not familiar with the codebase to know if this can have negative repercusions. Let me know if you'd like me to do a pull request.  
  
177: `class BOOST_LEAF_SYMBOL_VISIBLE BOOST_LEAF_ATTRIBUTE_NODISCARD result`  
623 `class BOOST_LEAF_SYMBOL_VISIBLE BOOST_LEAF_ATTRIBUTE_NODISCARD result<void>:`

---

## Comment 4

> Username: zajo  
> Created at: 2024-05-09 00:56:45 UTC  
> Updated at: 2024-05-09 00:58:21 UTC  
> Url: https://github.com/boostorg/leaf/issues/60#issuecomment-2101734480  

Woops. Thanks, yes a PR would be appreciated, that way it'll trigger the tests too.
