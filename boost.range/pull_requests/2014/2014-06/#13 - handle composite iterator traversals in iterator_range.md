# #13 handle composite iterator traversals in iterator_range [Merged]

> Username: ericniebler  
> Created at: 2014-06-19 07:50:41 UTC  
> Updated at: 2014-06-19 16:38:07 UTC  
> Merged at: 2014-06-19 09:31:15 UTC  
> Closed at: 2014-06-19 09:31:15 UTC  
> Url: https://github.com/boostorg/range/pull/13  

Commit [654ae838ee5228eec9006146cd1562c46d90f9d2] broke several Boost.Accumulators tests, and regressed functionality of `boost::iterator_range` for several kinds of iterators. The change assumes that `boost::iterator_traversal` returns one of a known set of traversal types. That's not true. It is merely required to return a type _convertible_ to one of the known traversal types. Many iterators have such a traversal category. In particular, many of the iterators produced by Boost.Iterators have a composite iterator category/traversal (see `boost::detail::iterator_category_with_traversal` in boost/iterator/detail/facade_iterator_category.hpp).  
  
This change makes accommodations for composite iterator categories. Tested with gcc-4.8 and msvc-12.0.  
  
The following is a program that fails without this change:  
  
```  
#include <functional>  
#include <boost/range/iterator_range.hpp>  
#include <boost/iterator/counting_iterator.hpp>  
#include <boost/iterator/transform_iterator.hpp>  
  
typedef  
    boost::transform_iterator<  
        std::binder1st<std::multiplies<std::size_t> >  
      , boost::counting_iterator<std::size_t>  
    >  
times2_iterator;  
  
int main()  
{  
    times2_iterator i =  
        boost::make_transform_iterator(  
            boost::make_counting_iterator(0u),  
            std::bind1st(std::multiplies<std::size_t>(), 2));  
    boost::iterator_range<times2_iterator> u(i,i);  
    auto x = u[4];  
}  
```

---
