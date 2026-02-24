# #288 - Compile error when using flat_map::extract_sequence with small_vector [Closed]

> Username: arthaud  
> Created at: 2024-09-02 14:35:24 UTC  
> Updated at: 2024-09-09 09:01:26 UTC  
> Closed at: 2024-09-09 08:54:23 UTC  
> Url: https://github.com/boostorg/container/issues/288  

Hi,  
  
My project started seeing compilation errors with boost 1.86.0, when using `flat_map::extract_sequence` in combination with `small_vector`.  
  
Here is a repro:  
```c++  
#include <boost/container/flat_map.hpp>  
#include <boost/container/small_vector.hpp>  
#include <iostream>  
  
using SmallVector = boost::container::small_vector<std::pair<int, int>, 1>;  
using FlatMap = boost::container::flat_map<int, int, std::less<int>, SmallVector>;  
  
int main()  
{  
    FlatMap map;  
    map.insert_or_assign(1, 1);  
  
    auto container = map.extract_sequence();  
    map.adopt_sequence(boost::container::ordered_unique_range,  
        std::move(container));  
  
    std::cout << "size: " << map.size() << "\n";  
    return 0;  
}  
```  
This results in:  
```  
/Users/arthaud/homebrew/opt/boost/include/boost/container/flat_map.hpp:77:11: error: non-const lvalue reference to type 'small_vector<[2 * ...], void, [...]>' cannot bind to a value of unrelated type 'small_vector<[2 * ...], boost::container::new_allocator<std::pair<int, int>>, [...]>'  
{  return s; }  
          ^  
/Users/arthaud/homebrew/opt/boost/include/boost/container/flat_map.hpp:1605:31: note: in instantiation of function template specialization 'boost::container::dtl::force<boost::container::small_vector<std::pair<int, int>, 1>, boost::container::small_vector<std::pair<int, int>, 1, boost::container::new_allocator<std::pair<int, int>>>>' requested here  
      return boost::move(dtl::force<sequence_type>(m_flat_tree.get_sequence_ref()));  
                              ^  
test.cpp:13:26: note: in instantiation of member function 'boost::container::flat_map<int, int, std::less<int>, boost::container::small_vector<std::pair<int, int>, 1>>::extract_sequence' requested here  
    auto container = map.extract_sequence();  
                         ^  
In file included from test.cpp:1:  
/Users/arthaud/homebrew/opt/boost/include/boost/container/flat_map.hpp:77:11: error: non-const lvalue reference to type 'small_vector<[2 * ...], boost::container::new_allocator<std::pair<int, int>>, [...]>' cannot bind to a value of unrelated type 'small_vector<[2 * ...], void, [...]>'  
{  return s; }  
          ^  
/Users/arthaud/homebrew/opt/boost/include/boost/container/flat_map.hpp:1627:90: note: in instantiation of function template specialization 'boost::container::dtl::force<boost::container::small_vector<std::pair<int, int>, 1, boost::container::new_allocator<std::pair<int, int>>>, boost::container::small_vector<std::pair<int, int>, 1>>' requested here  
   {  this->m_flat_tree.adopt_sequence_unique(ordered_unique_range_t(), boost::move(dtl::force<impl_sequence_type>(seq)));  }  
                                                                                         ^  
test.cpp:14:9: note: in instantiation of member function 'boost::container::flat_map<int, int, std::less<int>, boost::container::small_vector<std::pair<int, int>, 1>>::adopt_sequence' requested here  
    map.adopt_sequence(boost::container::ordered_unique_range,  
        ^  
2 errors generated.  
```  
This is also visible in CI here: https://github.com/facebook/mariana-trench/actions/runs/10595093691/job/29360092181  
  
I think this is related to `dtl::force<impl_sequence_type>`, but I didn't dig deeper.  
FYI this issue is not present with older version (for instance, boost 1.77.0).

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-09-09 09:01:24 UTC  
> Url: https://github.com/boostorg/container/issues/288#issuecomment-2337542752  

Many thanks for the issue. Trying to reproduce it I found that extract/adopt were not properly tested and in general, flat_map tests needed to test more combinations of types (move-only, etc) and underlying containers (small_vector, devector, etc.). I've committed those changes first (https://github.com/boostorg/container/commit/daa81ed5bb68a110d8a48c9fef66ba38621e8dfe) and then commit changes to fix your bug.  
  
So your bug report has been very useful, thanks!
