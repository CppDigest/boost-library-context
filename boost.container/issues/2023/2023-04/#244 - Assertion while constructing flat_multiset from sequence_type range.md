# #244 - Assertion while constructing flat_multiset from sequence_type range [Closed]

> Username: andiwand  
> Created at: 2023-04-27 12:39:38 UTC  
> Updated at: 2023-04-27 12:52:06 UTC  
> Closed at: 2023-04-27 12:52:06 UTC  
> Url: https://github.com/boostorg/container/issues/244  

Hello! I just ran into the following problem using boost 1.74 on Ubuntu 22.04.2 with gcc 11.3.0 and clang 14.  
  
After compiling and running the code attached bellow I get  
  
```bash  
$ clang++ reproduce.cpp -o reproduce && ./reproduce   
reproduce: /usr/include/boost/move/algo/adaptive_sort.hpp:452: bool boost::movelib::detail_adaptive::adaptive_sort_build_params(RandIt, const Unsigned, Compare, Unsigned &, Unsigned &, Unsigned &, Unsigned &, XBuf &) [RandIt = unsigned long *, Compare = boost::container::dtl::flat_tree_value_compare<std::less<unsigned long>, unsigned long, boost::move_detail::identity<unsigned long>>, Unsigned = unsigned long, XBuf = boost::movelib::adaptive_xbuf<unsigned long, unsigned long *, unsigned long>]: Assertion `collected < (n_min_ideal_keys+l_intbuf)' failed.  
Aborted (core dumped)  
```  
  
```c++  
#include <cstdint>  
#include <boost/container/flat_set.hpp>  
  
int main() {  
  using Value = std::uint64_t;  
  using Container = boost::container::flat_multiset<Value>;  
  using Sequence = Container::sequence_type;  
  
  Sequence sequence = {  
      1224979236083741952, 1224979373522738432, 1224979510961742080,  
      1224979510961745664, 1224979648400767232, 1729382394349332224,  
      1729382531788350208, 1729382669227395072, 1729382806666461440,  
      2089670364539261184, 2089670364539261440, 2089670501978375936,  
      2089670501978376192, 1729382669227400448, 1729382669227400448,  
      1729382669227400448, 1729382669227405056, 1729382669227405056,  
      1729382669227400448, 1729382669227400448, 1729382669227400448,  
      1729382669227400448, 1729382669227400448, 1729382669227400448,  
      1729382669227400448, 1729382669227400448, 1729382669227400448,  
      1729382669227400448, 1729382669227400448, 1729382669227400448,  
      1729382669227400448, 1729382669227405056, 1729382669227405056,  
      1729382669227405056, 1729382669227405056, 1729382669227405056,  
      1729382669227405056, 1729382669227405056, 1729382669227405056,  
      1729382669227405056, 1729382669227405056, 1729382669227405056,  
      1729382669227405056, 1729382669227405056, 1729382669227405056,  
      1729382669227405056, 1729382669227405056, 1729382669227405056,  
      1729382669227405056, 1729382669227405056, 1729382669227405056,  
      1729382669227405056, 1729382669227405056, 1729382669227405056,  
      1729382669227405056, 1729382669227405056, 1729382669227405056,  
      1729382669227405056};  
  
  Container constainer(sequence.begin(), sequence.end());  
  
  return 0;  
}  
```  
  
Observed in https://github.com/acts-project/acts/pull/2049/  
  
Godbolt https://godbolt.org/z/GrWrKvhTd

---

## Comment 1

> Username: andiwand  
> Created at: 2023-04-27 12:52:03 UTC  
> Url: https://github.com/boostorg/container/issues/244#issuecomment-1525643241  

Apparently this is fixed with boost 1.78  
  
Godbolt https://godbolt.org/z/M677sjroP
