# #83 - Indexing operator of iterator_range<counting_iterator<...>> produces undefined behavior [Open]

> Username: mkatliar  
> Created at: 2018-11-23 12:29:11 UTC  
> Updated at: 2019-09-20 11:08:00 UTC  
> Url: https://github.com/boostorg/range/issues/83  

Example:  
  
```c++  
#include <boost/iterator/counting_iterator.hpp>  
#include <boost/range/iterator_range.hpp>  
  
#include <iostream>  
#include <type_traits>  
  
int main(int, char **)  
{  
    using iterator = boost::counting_iterator<int>;  
    using iterator_range = boost::iterator_range<iterator>;  
  
    iterator first(0), last(3);  
    iterator_range range(first, last);  
  
    std::cout << "iterator::reference is a reference type: " << std::is_reference<iterator::reference>::value << std::endl;  
    std::cout << "iterator_range::reference is a reference type: " << std::is_reference<iterator_range::reference>::value << std::endl;  
  
    std::cout << "first[0] returns a reference type: " << std::is_reference<decltype(first[0])>::value << std::endl;  
    std::cout << "range[0] returns a reference type: " << std::is_reference<decltype(range[0])>::value << std::endl;  
      
    std::cout << "first[0] == " << first[0] << std::endl;  
    std::cout << "range[0] == " << range[0] << std::endl; // <-- segfault  
}  
```  
  
Output:  
```  
iterator::reference is a reference type: 1  
iterator_range::reference is a reference type: 1  
first[0] returns a reference type: 0  
range[0] returns a reference type: 1  
first[0] == 0  
Segmentation fault (core dumped)  
```  
  
Expected behavior: `range[0]` returns `0`.  
  
If Boost headers are copied to `./boost` and the program is compiled with g++ `-I.` option, the compiler produces the following warning:  
```  
In file included from ./boost/range/iterator_range.hpp:13,  
                 from test_counting_iterator_range_indexing.cpp:2:  
./boost/range/iterator_range_core.hpp: In instantiation of ‘boost::iterator_range_detail::iterator_range_base<IteratorT, boost::iterators::random_access_traversal_tag>::reference boost::iterator_range_detail::iterator_range_base<IteratorT, boost::iterators::random_access_traversal_tag>::operator[](boost::iterator_range_detail::iterator_range_base<IteratorT, boost::iterators::random_access_traversal_tag>::difference_type) const [with IteratorT = boost::iterators::counting_iterator<int>; boost::iterator_range_detail::iterator_range_base<IteratorT, boost::iterators::random_access_traversal_tag>::reference = const int&; boost::iterator_range_detail::iterator_range_base<IteratorT, boost::iterators::random_access_traversal_tag>::difference_type = long int]’:  
test_counting_iterator_range_indexing.cpp:23:43:   required from here  
./boost/range/iterator_range_core.hpp:391:32: warning: returning reference to temporary [-Wreturn-local-addr]  
         return this->m_Begin[at];  
                                ^  
```  
  
The reason for the segmentation fault is that `iterator_range::operator[]` returns a reference to a stack-allocated object returned by `counting_iterator::operator[]`.

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2018-11-24 07:19:01 UTC  
> Url: https://github.com/boostorg/range/issues/83#issuecomment-441349178  

What happens if you try the same test with `counting_range` from Boost.Range?

---

## Comment 2

> Username: morinmorin  
> Created at: 2019-09-20 11:08:00 UTC  
> Url: https://github.com/boostorg/range/issues/83#issuecomment-533510343  

Please don't use `counting_iterator`/`counting_range`. Use `irange(0, 3)`, instead.
