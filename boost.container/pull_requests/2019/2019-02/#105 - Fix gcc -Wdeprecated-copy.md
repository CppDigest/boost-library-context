# #105 Fix gcc -Wdeprecated-copy [Closed]

> Username: stac47  
> Created at: 2019-02-28 15:50:33 UTC  
> Updated at: 2019-03-05 11:16:27 UTC  
> Closed at: 2019-03-05 11:15:50 UTC  
> Url: https://github.com/boostorg/container/pull/105  

Hello,  
  
I spotted this while compiling FB Folly  
  
```  
/path/include/boost/container/flat_map.hpp:1530:12: error: implicitly-declared 'constexpr boost::container::vec_iterator<std::pair<int, int>*, false>& boost::container:  
:vec_iterator<std::pair<int, int>*, false>::operator=(const boost::container::vec_iterator<std::pair<int, int>*, false>&)' is deprecated [-Werror=deprecated-copy]  
 1530 |          i = insert(i, impl_value_type(k, ::boost::move(m.m_t)));  
In file included from /path/include/boost/container/detail/flat_tree.hpp:30,  
                 from /path/include/boost/container/flat_map.hpp:29,  
                 from ...  
/path/include/boost/container/vector.hpp:119:32: note: because 'boost::container::vec_iterator<std::pair<int, int>*, false>' has user-provided 'boost::container::vec_it  
erator<Pointer, IsConst>::vec_iterator(const boost::container::vec_iterator<Pointer, false>&) [with Pointer = std::pair<int, int>*; bool IsConst = false]'  
  119 |    BOOST_CONTAINER_FORCEINLINE vec_iterator(vec_iterator<Pointer, false> const& other) BOOST_NOEXCEPT_OR_NOTHROW  
      |                                ^~~~~~~~~~~~  
```  
  
As you don't use defaulted syntax ( = default) nor import <algorithm>, I did not dare using defaulted assignment operator syntax nor std::swap.  
  
Stac

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2019-03-05 11:16:26 UTC  
> Url: https://github.com/boostorg/container/pull/105#issuecomment-469642248  

The error was present in many container interator, many thanks for the report.

---
