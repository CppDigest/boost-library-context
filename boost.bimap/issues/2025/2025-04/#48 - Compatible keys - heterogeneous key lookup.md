# #48 - Compatible keys / heterogeneous key lookup [Closed]

> Username: MeanSquaredError  
> Created at: 2025-04-10 03:24:25 UTC  
> Updated at: 2025-04-10 16:56:49 UTC  
> Closed at: 2025-04-10 15:22:25 UTC  
> Url: https://github.com/boostorg/bimap/issues/48  

Currently `boost::bimap` does not support compatible keys, unlike other boost containers which do have compatible keys. The containers in C++20 and newer have a similar feature called "heterogeneous key lookup", but alas they don't have bimaps.  
  
It would be nice to have support for compatible keys in `boost::bimap` because they really simplify things, e.g. when the key is `std::string` and lookup is done via `std::string_view`. It seems possible in theory, given that the underlying `boost::multi_index` already supports compatible keys.

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-04-10 09:00:12 UTC  
> Url: https://github.com/boostorg/bimap/issues/48#issuecomment-2792056884  

Compatible keys are supported:  
  
https://godbolt.org/z/376jEzch6  
```cpp  
#include <boost/bimap.hpp>  
#include <boost/bimap/unordered_set_of.hpp>  
#include <iostream>  
#include <string>  
#include <string_view>  
  
struct transparent_hash  
{  
  std::size_t operator()(const std::string& x) const  
  {  
    std::cout << "std::string overload called\n";  
    return std::hash<std::string>()(x);  
  }  
  
  std::size_t operator()(const std::string_view& x) const  
  {  
    std::cout << "std::string_view overload called\n";  
    return std::hash<std::string_view>()(x);  
  }  
};  
  
int main()  
{  
  using namespace boost::bimaps;  
  boost::bimap<  
    unordered_set_of<std::string, transparent_hash, std::less<>>,  
    unordered_set_of<std::string, transparent_hash, std::less<>>  
  > bm;  
  
  bm.left.find(std::string("hello"));  
  bm.left.find(std::string_view("hello"));  
}  
```

---

## Comment 2

> Username: MeanSquaredError  
> Created at: 2025-04-10 15:22:25 UTC  
> Url: https://github.com/boostorg/bimap/issues/48#issuecomment-2794218952  

@joaquintides   
Thank you!  
  
In my experiments I was not using the `unordered_set_of` types and was trying to pass the hash/equality types (wrongly) as AP1, AP2, etc.

---

## Comment 3

> Username: MeanSquaredError  
> Created at: 2025-04-10 16:56:48 UTC  
> Url: https://github.com/boostorg/bimap/issues/48#issuecomment-2794545786  

Additionally, when conducting my experiments I was using boost-1.83 and it seems that it has problems with heterogeneous lookups as your sample program does not compile with 1.83, but it does compile with 1.87. I guess I will have to upgrade to the latest 1.87.
