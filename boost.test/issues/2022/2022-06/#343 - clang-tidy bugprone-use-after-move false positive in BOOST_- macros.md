# #343 - clang-tidy bugprone-use-after-move false positive in BOOST_* macros [Closed]

> Username: jspam  
> Created at: 2022-06-21 12:15:54 UTC  
> Updated at: 2024-10-04 17:48:18 UTC  
> Closed at: 2024-10-04 17:48:18 UTC  
> Url: https://github.com/boostorg/test/issues/343  

```cpp  
#include <boost/test/unit_test.hpp>  
#include <vector>  
  
void f(std::vector<int> p);  
  
int main() {  
  std::vector<int> v;  
  BOOST_CHECK_NO_THROW(f(std::move(v)));  
}   
```  
  
run `clang-tidy --extra-arg=-I/path/to/boost/include -checks=-*,bugprone-use-after-move test.cpp` (clang-tidy version 14.0.5)  
  
yields:  
```  
/tmp/test.cpp:8:36: warning: 'v' used after it was moved [bugprone-use-after-move]                                                             
  BOOST_CHECK_NO_THROW(f(std::move(v)));                                                                                                       
                                     
/tmp/test.cpp:8:26: note: move occurred here                                                                                                   
  BOOST_CHECK_NO_THROW(f(std::move(v)));                                                                                                       
                           
/tmp/test.cpp:8:36: note: the use happens in a later loop iteration than the move                                                              
  BOOST_CHECK_NO_THROW(f(std::move(v)));  
```  
  
Control flow analysis seems to be unable to see `do { … } while( ::boost::test_tools::tt_detail::dummy_cond() )` as a loop that runs only once. If I replace these loops by `do { … } while(0)` loops in `boost/test/tools/old/interface.hpp`, the warning disappears.  
  
Since it's probably unreasonable to expect clang-tidy to look into function calls, I wanted to first ask here – what is the purpose of `dummy_cond`? Would it be possible to use `do { … } while(0)` instead, which seems to be a common pattern?
