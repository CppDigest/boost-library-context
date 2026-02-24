# #44 - PolyLinePolygonWithHolesData::iteratorHoles is not LegacyIterator [Open]

> Username: fiesh  
> Created at: 2020-06-08 10:01:41 UTC  
> Updated at: 2020-06-08 10:01:41 UTC  
> Url: https://github.com/boostorg/polygon/issues/44  

The class template `PolyLinePolygonWithHolesData`'s member type `iteratorHoles` is not a legal `LegacyIterator` since there is no specialization of `std::iterator_traits` for it.  
  
This leads to the surprising behavior that code won't compile when the iterator is used with the standard library's algorithms, say `std::transform`, and the concept is correctly enforced (for example by compiling with `__GLIBCXX_DEBUG`).  
  
(There might be other iterators for which this is true as well?)
