# #44 - (split_)interval_map always preserve the values of the intersecting ranges during erase [Open]

> Username: justinasvd  
> Created at: 2024-03-19 07:33:34 UTC  
> Updated at: 2024-06-07 13:16:27 UTC  
> Url: https://github.com/boostorg/icl/issues/44  

Consider a test program  
  
```C++  
#include <boost/icl/split_interval_map.hpp>  
  
#include <iostream>  
  
struct foo {  
  int v;  
};  
  
inline bool operator==(const foo &lhs, const foo &rhs) noexcept {  
  return lhs.v == rhs.v;  
}  
  
inline std::ostream &operator<<(std::ostream &os, const foo &u) {  
  return os << u.v;  
}  
  
template <class T> struct dummy_ {  
  using first_argument_type = T;  
};  
  
int main() {  
  using interval_map =  
      boost::icl::split_interval_map<int, foo, boost::icl::partial_enricher,  
                                     std::less, dummy_, dummy_,  
                                     boost::icl::right_open_interval<int>>;  
  
  interval_map tst;  
  
  for (int i = 0; i < 10; i += 2) {  
    tst.insert(std::make_pair(boost::icl::right_open_interval<int>(i, i + 2),  
                              foo{10 * i}));  
  }  
  
  std::cout << "map: " << tst << std::endl;  
  
  const boost::icl::right_open_interval<int> test_range(3, 7);  
  
  std::cout << "after remove: ";  
  tst -= test_range;  
  std::cout << tst << std::endl;  
}  
```  
  
The output of this program is going to be  
  
```  
map: {([0,2)->0)([2,4)->20)([4,6)->40)([6,8)->60)([8,10)->80)}  
after remove: {([0,2)->0)([2,3)->20)([7,8)->60)([8,10)->80)}  
```  
  
This behavior is very surprising, because I would have expected that an `inter_sect` (in our case `dummy_`) f-tor would be used for the intersecting ranges [2,4) and [6,8). In my own use case, the correct output after removal should be   
  
`after remove: {([0,2)->0)([2,3)->`**0**`)([7,8)->`**0**`)([8,10)->80)}`  
  
But I see no way to achieve this with the current implementation of `erase(interval)`.

---

## Comment 1

> Username: justinasvd  
> Created at: 2024-03-19 07:56:12 UTC  
> Updated at: 2024-03-19 07:56:32 UTC  
> Url: https://github.com/boostorg/icl/issues/44#issuecomment-2006179839  

N.B. the behavior of   
  
```C++  
tst.erase(std::make_pair(test_range, foo{}));  
```  
  
is even more baffling, as it is a no-op!

---

## Comment 2

> Username: jofaber  
> Created at: 2024-06-07 13:16:26 UTC  
> Url: https://github.com/boostorg/icl/issues/44#issuecomment-2154818086  

Hi Justinas,  
subtracting an `interval` from an `Interval_map` using the `-=` operator is effectively erasing the all content form the `interval_map` within the interval `test_range`.  
If you'd like `intersection` to be propagated to values of the map, you need to combine two `interval_maps` that must have the same `codomain` type being a model of concept `Set`.  
You may take a look at example [user groups](https://www.boost.org/doc/libs/1_82_0/libs/icl/doc/html/boost_icl/examples/user_groups.html) for this kind of operations.  
Best regards,  
Joachim
