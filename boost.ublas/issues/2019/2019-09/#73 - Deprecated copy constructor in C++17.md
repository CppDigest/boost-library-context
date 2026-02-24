# #73 - Deprecated copy constructor in C++17 [Open]

> Username: NAThompson  
> Created at: 2019-09-01 17:49:43 UTC  
> Updated at: 2019-12-01 12:21:38 UTC  
> Url: https://github.com/boostorg/ublas/issues/73  

In `boost/libs/math/test/univariate_statistics_test.cpp`, we see the following warning using g++-9:  
  
```  
../../../boost/numeric/ublas/detail/iterator.hpp:268:27: warning: implicitly-declared ‘constexpr boost::numeric::ublas::vector<int, boost::numeric::ublas::unbounded_array<int, std::allocator<int> > >::iterator::iterator(const boost::numeric::ublas::vector<int, boost::numeric::ublas::unbounded_array<int, std::allocator<int> > >::iterator&)’ is deprecated [-Wdeprecated-copy]  
  268 |             return tmp -= n;  
```

---

## Comment 1

> Username: bassoy  
> Created at: 2019-12-01 12:21:31 UTC  
> Url: https://github.com/boostorg/ublas/issues/73#issuecomment-560101069  

Thanks @NAThompson. We will hopefully move soon to a different iterator implementation which is also valid for tensor. Meanwhile we might want to live with that warning.
