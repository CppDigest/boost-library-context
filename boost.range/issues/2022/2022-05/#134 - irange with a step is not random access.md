# #134 - irange with a step is not random access [Open]

> Username: dvirtz  
> Created at: 2022-05-02 12:33:53 UTC  
> Updated at: 2022-05-02 12:33:53 UTC  
> Url: https://github.com/boostorg/range/issues/134  

The [docs](https://www.boost.org/doc/libs/1_78_0/libs/range/doc/html/range/reference/ranges/irange.html) say that `irange` produces a random access range but that's not true for the strided version.  
  
The following code fails to compile:  
```cpp  
BOOST_RANGE_CONCEPT_ASSERT(( RandomAccessRangeConcept<decltype(boost::irange(0, 10))> ));  
BOOST_RANGE_CONCEPT_ASSERT(( RandomAccessRangeConcept<decltype(boost::irange(0, 10, 1))> )); // fails here  
```  
See https://gcc.godbolt.org/z/n4hqjj5bf  
  
The reason is that random access (in fact, even forward) concept requires the iterator to be default constructible which `integer_iterator_with_step` is not.
