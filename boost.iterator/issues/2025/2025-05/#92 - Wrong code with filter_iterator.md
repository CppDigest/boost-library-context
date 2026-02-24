# #92 - Wrong code with filter_iterator [Closed]

> Username: mglisse  
> Created at: 2025-05-24 19:56:08 UTC  
> Updated at: 2025-05-24 20:22:30 UTC  
> Closed at: 2025-05-24 20:22:18 UTC  
> Url: https://github.com/boostorg/iterator/issues/92  

https://github.com/boostorg/iterator/blob/6428e552e4ca50362193ba233cab6fea4270a450/include/boost/iterator/filter_iterator.hpp#L107  
Did you mean to write  
```diff  
-        super_t(t.base()), m_storage(t.m_storage.predicate(), m_storage.m_end)  
+        super_t(t.base()), m_storage(t.m_storage.predicate(), t.m_storage.m_end)  
```  
? (or maybe use the default copy constructor of storage)  
  
I noticed because some code in CGAL fails with the latest boost version  😞

---

## Comment 1

> Username: Lastique  
> Created at: 2025-05-24 20:22:28 UTC  
> Url: https://github.com/boostorg/iterator/issues/92#issuecomment-2907007258  

Thanks for the report.
