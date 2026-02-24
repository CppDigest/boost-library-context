# #287 - Boost 1.81.0 regression: path can no longer be overloaded with initializer_list<path> [Closed]

> Username: zhihaoy  
> Created at: 2023-05-17 02:44:39 UTC  
> Updated at: 2023-05-21 20:47:08 UTC  
> Closed at: 2023-05-21 20:47:08 UTC  
> Url: https://github.com/boostorg/filesystem/issues/287  

Consider this program:  
  
```cpp  
#include <boost/filesystem/path.hpp>  
#include <initializer_list>  
  
using X = boost::filesystem::path;  
static const X a, b;  
  
static std::string foo(std::initializer_list<X> paths)  
{  
    return {};  
}  
  
static std::string foo(const boost::filesystem::path &path)  
{  
    return foo({path});  
}  
  
int main()  
{  
    auto v = foo({a, b}); // failed since 1.81.0  
}  
```  
  
Godbolt: https://godbolt.org/z/hxKWqhsaW  
  
Persumbly broke by https://github.com/boostorg/filesystem/commit/b224703125ff51afeab82db469db48f615f7d1ff
