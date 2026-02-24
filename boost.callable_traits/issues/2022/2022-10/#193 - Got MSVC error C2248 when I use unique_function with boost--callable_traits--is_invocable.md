# #193 - Got MSVC error C2248 when I use unique_function with boost::callable_traits::is_invocable [Open]

> Username: redboltz  
> Created at: 2022-10-08 13:07:21 UTC  
> Updated at: 2022-10-12 02:26:05 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/193  

When I use `boost::callable_traits::is_invocable` with `fu2::unique_function` on msvc v19.33 or older, I got C2248 (cannot access private member) error.   
When I replace boost::callable_traits::is_invocable with std::is_invocable (since C++17), then the error is disappeared.  
  
```cpp  
#include <function2/function2.hpp>  
#include <boost/callable_traits.hpp>  
#include <type_traits>  
  
using unique_func_t = fu2::unique_function<void()>;  
  
int main() {  
    // no error with std::is_invocable  
    static_assert(std::is_invocable<unique_func_t>::value);  
    // error C2248 with boost::callable_traits::is_invocable  
    static_assert(boost::callable_traits::is_invocable<unique_func_t>::value);  
}  
```  
  
Godbolt link: https://godbolt.org/z/vM19nhfv7  
  
At first, I report the issue to function2.  
See https://github.com/Naios/function2/issues/54  
  
The issue happens the combination of `boost::callable_traits::is_invocable`, `fu2::unique_function`, and msvc v19.33 or older.  
It is complecated for me but fortunately, we can reproduce the issue on godbolt.  
  
So I report the issue here too.

---

## Comment 1

> Username: badair  
> Created at: 2022-10-12 02:26:05 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/193#issuecomment-1275500915  

Thanks for reporting. That's an interesting case I haven't seen before -- looks like it works correctly on GCC and Clang? Unfortunately, I don't have the bandwidth to dig into this right now, but I'll leave this issue open for a rainy day.
