# #100 - Compile error on Green Hills: container_detail::flat_tree has no member "insert" [Closed]

> Username: Breakthrough  
> Created at: 2019-01-28 19:04:07 UTC  
> Updated at: 2019-01-30 20:57:54 UTC  
> Closed at: 2019-01-30 20:57:54 UTC  
> Url: https://github.com/boostorg/container/issues/100  

The following lines cause issue on GHS C++14 compiler (lines 1218 and 1225):  
  
https://github.com/boostorg/container/blob/83bb62fed3aca489e447e394f3b23c272712ecb7/include/boost/container/detail/flat_tree.hpp#L1215-L1227  
  
Specifically, yields the following errors:  
  
```  
\boost_1_66_0\boost/container/detail/flat_tree.hpp", line 1131: error #135:  
          class template "boost::container::container_detail::flat_tree<Value,  
          KeyOfValue, Compare, AllocatorOrContainer>" has no member "insert"  
        this->insert( boost::make_move_iterator(source.begin())  
              ^  
  
\boost_1_66_0\boost/container/detail/flat_tree.hpp", line 1138: error #135:  
          class template "boost::container::container_detail::flat_tree<Value,  
          KeyOfValue, Compare, AllocatorOrContainer>" has no member "insert"  
        this->insert( boost::make_move_iterator(source.begin())  
              ^  
```  
  
I'm not 100% sure on the solution, but think it might be replacing this->insert with this->insert_unique/insert_equal?  
  
Thank you!

---

## Comment 1

> Username: Breakthrough  
> Created at: 2019-01-29 00:57:57 UTC  
> Updated at: 2019-01-29 00:58:26 UTC  
> Url: https://github.com/boostorg/container/issues/100#issuecomment-458364088  

I've submitted a PR (#101) with my proposed fix (namely, replacing the two calls to insert() with insert_unique() and insert_equal(), respectively).  It passes all boost::container unit tests, but wanted to verify that this was the actual intent of these functions, and would like to know if this is a valid solution (or if another approach should be considered instead).  
  
Thank you!
