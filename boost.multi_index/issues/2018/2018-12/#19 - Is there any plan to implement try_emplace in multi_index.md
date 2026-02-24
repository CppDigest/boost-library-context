# #19 - Is there any plan to implement try_emplace in multi_index? [Closed]

> Username: gri1n  
> Created at: 2018-12-14 08:08:47 UTC  
> Updated at: 2022-05-18 11:49:22 UTC  
> Closed at: 2018-12-14 09:18:34 UTC  
> Url: https://github.com/boostorg/multi_index/issues/19  

Both insert_or_assign and try_emplace would be really handy on multi_index but especially try_emplace. Are they going to be implemented any time soon?

---

## Comment 1

> Username: joaquintides  
> Created at: 2018-12-14 09:18:34 UTC  
> Updated at: 2018-12-14 09:20:38 UTC  
> Url: https://github.com/boostorg/multi_index/issues/19#issuecomment-447264822  

These member functions have been defined for `std` **maps** and not for **sets** because in the former case there is a separation between the key and the associated value, which are conflated in the latter case.  
  
Boost.MultiIndex behaves in this respect like a set, that is, the key is part of the value (obtained with the corresponding key extractor) and not the first member of a `std::pair`. So, ` insert_or_assign` and `try_emplace` do not make sense here.  
  
Just for the fun of it, if you are using a `multi_index_container` where the elements are indeed `std::pair`s and the key happens to be the first member, you can roll out your own `try_or_emplace` as follows:  
  
```  
#include <utility>  
#include <tuple>  
#include <type_traits>  
  
template<typename Container,typename Key,typename... Args>  
std::pair<typename Container::iterator,bool>  
try_emplace(Container& c,Key&& k,Args&&... args)  
{  
  static_assert(  
    std::is_constructible<  
      typename Container::value_type,  
      std::piecewise_construct_t,  
      std::tuple<Key&&>,  
      std::tuple<Args&&...>  
    >::value,  
    "value_type must be std::pair or behave as such with respect to piecewise "  
    "contruction with key and args...");  
      
  auto it=c.find(k);  
  if(it!=c.end())return {it,false};  
  else           return c.emplace(  
    std::piecewise_construct,std::forward_as_tuple(std::forward<Key>(k)),  
    std::forward_as_tuple(std::forward<Args>(args)...));  
}  
  
#include <boost/multi_index_container.hpp>  
#include <boost/multi_index/ordered_index.hpp>  
#include <boost/multi_index/member.hpp>  
#include <cassert>  
  
using namespace boost::multi_index;  
using element=std::pair<int,int>;  
using container=multi_index_container<  
  element,  
  indexed_by<  
    ordered_unique<member<element,int,&element::first>>  
  >  
>;  
  
int main()  
{  
  container c;  
  auto p1=try_emplace(c,0,1);  
  assert(p1.first->second==1&&p1.second==true);  
  auto p2=try_emplace(c,0,2);  
  assert(p2.first->second==1&&p2.second==false);  
  auto p3=try_emplace(c,1,3);  
  assert(p3.first->second==3&&p3.second==true);  
}  
  
```
