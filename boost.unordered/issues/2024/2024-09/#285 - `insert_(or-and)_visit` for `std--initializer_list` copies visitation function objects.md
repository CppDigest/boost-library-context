# #285 - `insert_(or|and)_[c]visit` for `std::initializer_list` copies visitation function objects [Closed]

> Username: joaquintides  
> Created at: 2024-09-14 10:16:14 UTC  
> Updated at: 2024-10-08 15:35:59 UTC  
> Closed at: 2024-10-08 15:35:59 UTC  
> Url: https://github.com/boostorg/unordered/issues/285  

(Concurrent containers.) The following:  
  
https://github.com/boostorg/unordered/blob/4c0aea983ebec4a9491381e7e4f3a9da844d19ba/include/boost/unordered/concurrent_flat_map.hpp#L482-L487  
  
copies `f`, thus unnecessarily imposing copyability on `F`. By contrast, other `concurrent_flat_map` visitation-enabled operations don't have such requirement since they delegate on `concurrent_table` functions accepting the visitation function by forwarding reference:  
  
```cpp  
#include <boost/unordered/concurrent_flat_map.hpp>  
  
struct NCF  
{  
  NCF() = default;  
  NCF(const NCF&) = delete;  
  NCF(NCF&&) = default;  
  
  template<typename T>  
  void operator()(const T&) const {}  
};  
  
int main()  
{  
  boost::concurrent_flat_map<int, int> m;  
  
  // ok  
  m.insert_or_visit({0, 0}, NCF{});   
  
  // 'NCF::NCF(const NCF &)': attempting to reference a deleted function  
  m.insert_or_visit({{0, 0}, {1, 0}}, NCF{});  
}  
```  
  
This can be fixed by wrapping `f` into a `std::reference_wrapper`. Documentation should also reflect the change. This should wait for #283 to be merged because `*_and_[c]visit` operations are also affected (on purpose, in anticipation of this issue publication). Applies to all concurrent containers.
