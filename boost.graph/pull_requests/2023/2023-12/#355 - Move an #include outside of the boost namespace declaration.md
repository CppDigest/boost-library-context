# #355 Move an #include outside of the boost namespace declaration. [Merged]

> Username: vslashg  
> Created at: 2023-12-02 00:03:20 UTC  
> Updated at: 2023-12-02 03:55:42 UTC  
> Merged at: 2023-12-02 03:54:14 UTC  
> Closed at: 2023-12-02 03:54:14 UTC  
> Url: https://github.com/boostorg/graph/pull/355  

iteration_macros.hpp itself includes &lt;utility&gt;, so this inclusion ends up effectively doing  
  
```  
namespace boost {  
#include <utility>  
}  
```  
  
which is disallowed per [using.headers].

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2023-12-02 03:55:41 UTC  
> Url: https://github.com/boostorg/graph/pull/355#issuecomment-1837028606  

Thanks! It's easy to merge straightforward fixes like this!

---
