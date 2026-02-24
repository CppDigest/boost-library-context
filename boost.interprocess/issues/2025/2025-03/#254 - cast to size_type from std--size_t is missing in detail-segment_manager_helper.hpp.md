# #254 - cast to size_type from std::size_t is missing in detail/segment_manager_helper.hpp [Closed]

> Username: wanghan02  
> Created at: 2025-03-11 10:10:42 UTC  
> Updated at: 2025-03-13 11:34:19 UTC  
> Closed at: 2025-03-13 11:34:14 UTC  
> Url: https://github.com/boostorg/interprocess/issues/254  

As in the diff below. Cast to size_type from std::size_t is applied in similar places but missing here.  
  
```  
--- ./boost_1_87_0/boost/interprocess/detail/segment_manager_helper.hpp  
+++ ./boost_1_87_0/boost/interprocess/detail/segment_manager_helper.hpp  
@@ -392,7 +392,7 @@  
   
    size_type name_length_offset() const  
    {  
-      return this->value_offset() + get_rounded_size(m_value_bytes, ::boost::move_detail::alignment_of<name_len_t>::value);  
+      return this->value_offset() + get_rounded_size(m_value_bytes, size_type(::boost::move_detail::alignment_of<name_len_t>::value));  
    }  
 };  
   
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-03-13 11:34:15 UTC  
> Url: https://github.com/boostorg/interprocess/issues/254#issuecomment-2720944400  

Thanks. This was recently fixed in:  
  
https://github.com/boostorg/interprocess/commit/061bc6f8dfc2af90d9996a77317b21398f6bdda5  
  
issue:  
  
https://github.com/boostorg/interprocess/issues/248
