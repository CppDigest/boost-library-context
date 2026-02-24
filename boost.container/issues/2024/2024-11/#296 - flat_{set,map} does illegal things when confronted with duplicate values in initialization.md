# #296 - flat_{set,map} does illegal things when confronted with duplicate values in initialization [Closed]

> Username: jcelerier  
> Created at: 2024-11-23 19:08:06 UTC  
> Updated at: 2024-11-25 13:31:12 UTC  
> Closed at: 2024-11-25 13:31:10 UTC  
> Url: https://github.com/boostorg/container/issues/296  

boost 1.86  
  
Repro:  
  
```c++  
#include <boost/container/flat_set.hpp>  
#include <vector>  
  
int main()  
{  
  boost::container::flat_set<int, std::less<void>, std::vector<int>> v {  1, 2, 3, 1 };  
  return v.size();  
}  
```  
  
```bash  
$  g++ wut.cpp  -D_GLIBCXX_DEBUG=1   
$ ./a.out  
/usr/include/c++/14/debug/safe_iterator.h:570:  
In function:  
    bool gnu_debug::operator!=(const _Safe_iterator<gnu_cxx::  
    normal_iterator<int*, std::vector<int, std::allocator<int> > >, std::  
    debug::vector<int>, std::forward_iterator_tag>::_Self&, const   
    _Safe_iterator<gnu_cxx::normal_iterator<int*, std::vector<int,   
    std::allocator<int> > >, std::debug::vector<int>,   
    std::forward_iterator_tag>::_Self&)  
  
Error: attempt to compare a dereferenceable (start-of-sequence) iterator to   
a singular iterator.  
  
Objects involved in the operation:  
    iterator "lhs" @ 0xffffe24c8130 {  
      type = gnu_cxx::normal_iterator<int*, std::vector<int, std::allocator<int> > > (mutable iterator);  
      state = dereferenceable (start-of-sequence);  
      references sequence with type 'std::debug::vector<int, std::allocator<int> >' @ 0xffffe24c83e8  
    }  
    iterator "rhs" @ 0xffffe24c8108 {  
      type = gnu_cxx::normal_iterator<int*, std::vector<int, std::allocator<int> > > (mutable iterator);  
      state = singular;  
      references sequence with type 'std::debug::vector<int, std::allocator<int> >' @ 0xffffe24c83e8  
    }  
Abandon (core dumped)  
```  
  
Relevant backtrace:  
  
```  
#5  0x0000000000403868 in __gnu_debug::operator!= (__lhs=1, __rhs=invalid iterator) at /usr/include/c++/14/debug/safe_iterator.h:570  
#6  0x00000000004022e8 in boost::container::dtl::flat_tree<int, boost::move_detail::identity<int>, std::less<void>, std::__debug::vector<int, std::allocator<int> > >::insert_unique<int const*> (this=0xffffffffe668, first=0x4456b8 <C.0.0>, last=0x4456c8 <vtable for __gnu_cxx::__concurrence_unlock_error>)  
    at /usr/include/boost/container/detail/flat_tree.hpp:934  
#7  0x0000000000401fb8 in boost::container::dtl::flat_tree<int, boost::move_detail::identity<int>, std::less<void>, std::__debug::vector<int, std::allocator<int> > >::priv_range_insertion_construct<int const*> (this=0xffffffffe668, unique_insertion=true, first=0x4456b8 <C.0.0>,   
    last=0x4456c8 <vtable for __gnu_cxx::__concurrence_unlock_error>) at /usr/include/boost/container/detail/flat_tree.hpp:1488  
#8  0x0000000000401490 in boost::container::dtl::flat_tree<int, boost::move_detail::identity<int>, std::less<void>, std::__debug::vector<int, std::allocator<int> > >::flat_tree<int const*> (this=0xffffffffe668, unique_insertion=true, first=0x4456b8 <C.0.0>,   
    last=0x4456c8 <vtable for __gnu_cxx::__concurrence_unlock_error>) at /usr/include/boost/container/detail/flat_tree.hpp:691  
```

---

## Comment 1

> Username: jcelerier  
> Created at: 2024-11-23 19:15:42 UTC  
> Url: https://github.com/boostorg/container/issues/296#issuecomment-2495626057  

From the code it looks like it expects the input to be unique & sorted but the documentation does not state this requirement for the intializer_list constructor:  
  
```  
  
   //! <b>Effects</b>: Constructs an empty flat_map and  
   //! inserts elements from the range [il.begin() ,il.end()).  
   //!  
   //! <b>Complexity</b>: Linear in N if the range [il.begin(), il.end()) is already sorted using  
   //! the predicate and otherwise N logN, where N is last - first.  
   BOOST_CONTAINER_FORCEINLINE flat_map(std::initializer_list<value_type> il)  
     : m_flat_tree( true  
                  , dtl::force<impl_initializer_list>(il).begin()  
                  , dtl::force<impl_initializer_list>(il).end())  
   {}  
```

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-11-24 21:32:26 UTC  
> Url: https://github.com/boostorg/container/issues/296#issuecomment-2496245954  

I think the problem is that that iterator comparison is performed with potentially invalidated iterators, and the iterator debugging machinery detects that. Please confirm that the following commit, also fixes your problem: https://github.com/boostorg/container/commit/3e70ecfa80f94689e5917e18ac861adef4a1840f

---

## Comment 3

> Username: jcelerier  
> Created at: 2024-11-24 22:41:21 UTC  
> Url: https://github.com/boostorg/container/issues/296#issuecomment-2496291093  

seems to fix the issue on my end indeed ! :)

---

## Comment 4

> Username: igaztanaga  
> Created at: 2024-11-25 13:31:11 UTC  
> Url: https://github.com/boostorg/container/issues/296#issuecomment-2498032080  

Great. Closing the bug as resolved!
