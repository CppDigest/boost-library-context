# #25 - Question: default iterator are different? [Closed]

> Username: filcuc  
> Created at: 2019-07-09 20:30:59 UTC  
> Updated at: 2019-07-14 15:42:26 UTC  
> Closed at: 2019-07-14 10:22:51 UTC  
> Url: https://github.com/boostorg/multi_index/issues/25  

```  
#include <iostream>  
#include <cstdlib>  
#include <boost/multi_index_container.hpp>  
#include <boost/multi_index/ordered_index.hpp>  
#include <boost/multi_index/identity.hpp>  
#include <boost/multi_index/member.hpp>  
#include <boost/multi_index/tag.hpp>  
#include <boost/multi_index/hashed_index.hpp>  
  
namespace bmi = boost::multi_index;  
  
  
namespace Node {  
    using Id = std::int64_t;  
    struct Tag {};  
}  
  
  
namespace Edge {  
    using Id = std::int64_t;  
    struct Tag {};  
}  
  
  
struct IndexById {};  
struct IndexBySource {};  
struct IndexByTarget {};  
  
  
  
    struct EdgeData {  
        Edge::Id id;  
        bool deleted = false;  
        Node::Id source;  
        Node::Id target;  
    };  
  
    using EdgeDataContainer = boost::multi_index_container<  
    EdgeData,  
    bmi::indexed_by<  
    bmi::ordered_unique<bmi::tag<IndexById>, bmi::member<EdgeData, Edge::Id, &EdgeData::id>>,  
    bmi::ordered_non_unique<bmi::tag<IndexBySource>, bmi::member<EdgeData, Node::Id, &EdgeData::source>>,  
    bmi::ordered_non_unique<bmi::tag<IndexByTarget>, bmi::member<EdgeData, Node::Id, &EdgeData::target>>  
    >  
    >;  
      
        using EdgeDataContainerByIdIndex = typename bmi::index<EdgeDataContainer, IndexById>::type;  
    using EdgeDataContainerBySourceIndex = typename bmi::index<EdgeDataContainer, IndexBySource>::type;  
    using EdgeDataContainerByTargetIndex = typename bmi::index<EdgeDataContainer, IndexByTarget>::type;  
  
int main()  
{  
    EdgeDataContainerByIdIndex::const_iterator it_1, it_2;  
      
    std::cout << (it_1 == it_2 ? "true" : "false") << std::endl;  
}  
```  
  
Is there a reason why this print false? i would expected to print true since the iterator are both default constructed

---

## Comment 1

> Username: joaquintides  
> Created at: 2019-07-14 10:22:51 UTC  
> Url: https://github.com/boostorg/multi_index/issues/25#issuecomment-511190617  

Hi Filippo,  
  
A default-initialized iterator is in a so-called singular state, where the only thing you can validly do with it is, basically, assign it a non-singular value (i.e. the value of some other iterator that actually points to a sequence). If you like standardese, details can be found [here](http://eel.is/c++draft/iterator.requirements.general#7). So your program is engendering undefined behavior because you're comparing two _singular values_ (`it_1` and `it_2`): in principle, the program can output `true`, `false`, or wipe up your hard disk, as the old joke about UB goes.  
  
I've run your program in [Wandbox](https://wandbox.org/permlink/yP9yHJZSklKHU6jA) with [Boost.MultiIndex safe mode on](https://www.boost.org/libs/multi_index/doc/tutorial/debug.html#safe_mode) and the result is:  
  
```  
a.out: /usr/local/include/boost/multi_index/detail/safe_mode.hpp:468:  
  bool boost::multi_index::safe_mode::safe_iterator<Iterator, Container>::equal(  
     const boost::multi_index::safe_mode::safe_iterator<Iterator, Container>&) const [with ...]:  
  Assertion `safe_mode::check_valid_iterator(*this)' failed.  
  ...  
```  
This issue is not exclusive to Boost.MultiIndex, and messing with singular iterators raises UB with any standard container. For example:  
  
```  
#include <list>  
#include <iostream>  
  
int main()  
{  
  std::list<int>::iterator it1,it2;  
  std::cout<<(it1==it2)<<"\n";  
}  
```  
is detected as invalid when run in [Wandbox](https://wandbox.org/permlink/XBrwec2t3gC8QCG2) in `_GLIBCXX_DEBUG` mode:  
  
```  
/opt/wandbox/gcc-9.1.0/include/c++/9.1.0/debug/safe_iterator.h:454:  
In function:  
    bool __gnu_debug::operator==(const _Self&, const _Self&)  
  
Error: attempt to compare a singular iterator to a singular iterator.  
...  
```

---

## Comment 2

> Username: filcuc  
> Created at: 2019-07-14 13:54:38 UTC  
> Url: https://github.com/boostorg/multi_index/issues/25#issuecomment-511204872  

@joaquintides  thank you for the insightful answer!! Ok than I will use an optional<T> for handling not initialized iterators

---

## Comment 3

> Username: joaquintides  
> Created at: 2019-07-14 15:42:26 UTC  
> Url: https://github.com/boostorg/multi_index/issues/25#issuecomment-511213213  

Glad to be helpful. You may also want to ask yourself why your program needs to handle uninitialized entities --maybe you can refactor your design so as to get rid of this requirement.
