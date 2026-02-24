# #54 - error: there are no arguments to 'BOOST_STATIC_ASSERT' that depend on a template parameter, so a declaration of 'BOOST_STATIC_ASSERT' must be available [-fpermissive] [Open]

> Username: arvid-norlander  
> Created at: 2025-07-03 14:34:40 UTC  
> Updated at: 2025-07-07 00:22:07 UTC  
> Url: https://github.com/boostorg/heap/issues/54  

Trying to package boost.heap for Bazel Central Registry for 1.88.0 I get this:  
  
```  
external/boost.heap~/include/boost/heap/detail/tree_iterator.hpp: In constructor 'boost::heap::detail::tree_iterator<Node, ValueType, Alloc, ValueExtractor, PointerExtractor, check_null_pointer, ordered_iterator, ValueCompare>::tree_iterator(NodePointerIterator, NodePointerIterator, const Node*, const ValueCompare&)':  
external/boost.heap~/include/boost/heap/detail/tree_iterator.hpp:198:9: error: there are no arguments to 'BOOST_STATIC_ASSERT' that depend on a template parameter, so a declaration of 'BOOST_STATIC_ASSERT' must be available [-fpermissive]  
  198 |         BOOST_STATIC_ASSERT( ordered_iterator );  
      |         ^~~~~~~~~~~~~~~~~~~  
external/boost.heap~/include/boost/heap/detail/tree_iterator.hpp:198:9: note: (if you use '-fpermissive', G++ will accept your code, but allowing the use of an undeclared name is deprecated)  
```  
  
Bazel Central Registry builds are set up to check that each header compiles standalone, and for 1.87.0 this worked. For 1.88.0 there seems to be this new error. I would guess it is simply a missing include in `include/boost/heap/detail/tree_iterator.hpp`.

---

## Comment 1

> Username: timblechmann  
> Created at: 2025-07-07 00:22:07 UTC  
> Url: https://github.com/boostorg/heap/issues/54#issuecomment-3043171804  

> Bazel Central Registry builds are set up to check that each header compiles standalone  
  
note that this is a rather strict policy: headers in `detail` are internal headers, that users should not include.
