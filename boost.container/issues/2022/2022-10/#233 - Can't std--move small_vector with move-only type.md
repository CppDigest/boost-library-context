# #233 - Can't std::move small_vector with move-only type [Closed]

> Username: JVApen  
> Created at: 2022-10-31 14:08:52 UTC  
> Updated at: 2023-03-13 09:08:10 UTC  
> Closed at: 2023-03-13 09:07:10 UTC  
> Url: https://github.com/boostorg/container/issues/233  

Code on [Compiler Explorer](https://compiler-explorer.com/z/s6fEE4hvE):  
````  
#include <boost/container/small_vector.hpp>  
#include <memory>  
  
struct C  
{  
    C(int);  
    ~C() = default;  
  
    C(const C &) = delete;  
    C(C &&) noexcept = default;  
    C &operator=(const C &)= delete;  
    C &operator=(C &&)= delete;  
};  
  
using V = boost::container::small_vector<C, 1>;  
  
auto f(V &&v)   
{  
    return std::make_shared<V>(std::move(v));  
}  
````  
  
Error:  
````  
 error: overload resolution selected deleted operator '='  
      *r = *f;  
      ~~ ^ ~~  
````  
  
Within a move constructor, I don't expect `operator=` to be used, as it should only be calling the move constructor of the class.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2023-03-13 09:08:10 UTC  
> Url: https://github.com/boostorg/container/issues/233#issuecomment-1465760999  

Many thanks for the report!
