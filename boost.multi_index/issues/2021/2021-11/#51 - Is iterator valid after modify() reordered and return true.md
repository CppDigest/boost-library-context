# #51 - Is iterator valid after modify() reordered and return true [Closed]

> Username: redboltz  
> Created at: 2021-11-26 11:16:47 UTC  
> Updated at: 2021-11-26 11:41:26 UTC  
> Closed at: 2021-11-26 11:41:26 UTC  
> Url: https://github.com/boostorg/multi_index/issues/51  

According to the document:  
https://www.boost.org/doc/libs/1_77_0/libs/multi_index/doc/reference/seq_indices.html  
  
> template<typename Modifier> bool modify(iterator position,Modifier mod);  
> ...  
> Postconditions: Validity of position is preserved if the operation succeeds.  
  
I wrote the following PoC code.  
Before modify() calling, it2 is point to the value order=2.  
After modify() is called, the order is updated to 10.  
it2 is now point to updated element. (order 10)  
  
The it2 is now point to the last element because the order is the biggest value(10).  
So `assert(it2 == it10)` should success.  
  
it3 is also modified by modify() call. it3 is got **before it2 's modification**. it3 isn't effected by it2 modification.  
  
Am I understanding correctly?  
  
The actual usecase is calling multiple async APIs (Boost.Asio) on the single thread.  
And async completion handler captures the iterator that points to multi index element.  
The multi_index container could be reordered by modify but never added and removed in my usecase.  
  
```cpp  
#include <iostream>  
#include <boost/multi_index_container.hpp>  
#include <boost/multi_index/ordered_index.hpp>  
#include <boost/multi_index/key.hpp>  
  
namespace mi = boost::multi_index;  
  
struct my {  
    int order;  
    int data;  
};  
  
using mi_my = mi::multi_index_container<  
    my,  
    mi::indexed_by<  
        mi::ordered_unique<  
            mi::key<&my::order>  
        >  
    >  
>;  
  
int main() {  
    mi_my m;  
    m.emplace(my{3, 30});  
    m.emplace(my{1, 10});  
    m.emplace(my{2, 20});  
      
    auto it = m.begin();  
      
    auto it1 = it++;  
    auto it2 = it++;  
    auto it3 = it++;  
      
    std::cout << it1->order << std::endl;  
    std::cout << it2->order << std::endl;  
    std::cout << it3->order << std::endl;  
    std::cout << "---" << std::endl;  
  
    {  
        bool ret = m.modify(it2, [&](auto& e){ e.order = 10; });  
        assert(ret);  
    }  
    {  
        bool ret = m.modify(it3, [&](auto& e){ e.order = 0; });  
        assert(ret);  
    }  
      
    std::cout << it1->order << std::endl;  
    std::cout << it2->order << std::endl;  
    std::cout << it3->order << std::endl;  
    std::cout << "---" << std::endl;  
      
    for (auto const& e : m) std::cout << e.order << std::endl;  
    auto it10 = --m.end();  
  
    assert(it2 == it10);  
}     
```  
  
https://wandbox.org/permlink/k0CWgffmiexV4mVt

---

## Comment 1

> Username: joaquintides  
> Created at: 2021-11-26 11:38:27 UTC  
> Url: https://github.com/boostorg/multi_index/issues/51#issuecomment-979913568  

Hi Takatoshi,  
  
Your assumptions are all correct. To summarize, an iterator to a given element will point always to that element regardless of whether the element or other elements are modified or whether other elements are inserted or erased. Naturally, the position of the iterator/element in the index will vary as these operations take place, but not their pointing consistently to the same element.  
  
All clear? Best,

---

## Comment 2

> Username: redboltz  
> Created at: 2021-11-26 11:41:26 UTC  
> Url: https://github.com/boostorg/multi_index/issues/51#issuecomment-979916438  

Thank you for the prompt answer!
