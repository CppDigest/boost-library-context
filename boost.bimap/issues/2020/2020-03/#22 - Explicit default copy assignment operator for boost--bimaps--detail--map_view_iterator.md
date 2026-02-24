# #22 - Explicit default copy assignment operator for boost::bimaps::detail::map_view_iterator? [Closed]

> Username: garethsb  
> Created at: 2020-03-20 10:40:38 UTC  
> Updated at: 2020-05-22 14:10:21 UTC  
> Closed at: 2020-05-22 14:10:21 UTC  
> Url: https://github.com/boostorg/bimap/issues/22  

Adding an explicit default copy assignment operator to `boost::bimaps::detail::map_view_iterator` suppresses a warning from GCC 9.1+ about https://en.cppreference.com/w/cpp/language/copy_assignment#Implicitly-defined_copy_assignment_operator due to the explicit copy constructor.  
  
```patch  
+    map_view_iterator& operator=(map_view_iterator const& iter) = default;  
```  
  
Is that the right thing to do?

---

## Comment 1

> Username: glenfe  
> Created at: 2020-05-22 13:32:41 UTC  
> Url: https://github.com/boostorg/bimap/issues/22#issuecomment-632692511  

Bimap needs to support C++03 so that would have to be added conditionally only when the newer compilers are detected. But I don't think that's the best solution. Instead we should get rid of the explicit copy constructor, so that both copy constructor and assignment operators are implicitly provided.

---

## Comment 2

> Username: glenfe  
> Created at: 2020-05-22 14:10:20 UTC  
> Url: https://github.com/boostorg/bimap/issues/22#issuecomment-632713043  

Addressed in 6b4d000a4fb38978c87a48c7c22344e61a33290f.
