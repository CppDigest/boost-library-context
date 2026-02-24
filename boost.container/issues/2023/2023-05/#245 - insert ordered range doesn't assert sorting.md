# #245 - [flat_tree] insert ordered range doesn't assert sorting [Closed]

> Username: timblechmann  
> Created at: 2023-05-11 14:31:35 UTC  
> Updated at: 2024-06-14 11:44:28 UTC  
> Closed at: 2024-06-14 11:43:53 UTC  
> Url: https://github.com/boostorg/container/issues/245  

`void insert(ordered_unique_range_t, InputIterator first, InputIterator last)` and friends don't validate that the range is sorted, while the corresponding constructor does.  
  
it would be great if this could be validated. i've done some quick checks with:  
```  
diff --git a/include/boost/container/detail/flat_tree.hpp b/include/boost/container/detail/flat_tree.hpp  
index 5a14826..bf15564 100644  
--- a/include/boost/container/detail/flat_tree.hpp  
+++ b/include/boost/container/detail/flat_tree.hpp  
@@ -952,20 +952,22 @@ class flat_tree  
   
    //Ordered  
   
    template <class InIt>  
    void insert_equal(ordered_range_t, InIt first, InIt last)  
    {  
+      BOOST_ASSERT((is_sorted)(first, last, this->priv_value_comp()));  
       const bool value = boost::container::dtl::  
          has_member_function_callable_with_merge_unique<container_type, InIt, InIt, value_compare>::value;  
       (flat_tree_merge_equal)(this->m_data.m_seq, first, last, this->priv_value_comp(), dtl::bool_<value>());  
    }  
   
    template <class InIt>  
    void insert_unique(ordered_unique_range_t, InIt first, InIt last)  
    {  
+      BOOST_ASSERT((is_sorted)(first, last, this->priv_value_comp()));  
       const bool value = boost::container::dtl::  
          has_member_function_callable_with_merge_unique<container_type, InIt, InIt, value_compare>::value;  
       (flat_tree_merge_unique)(this->m_data.m_seq, first, last, this->priv_value_comp(), dtl::bool_<value>());  
    }  
   
    #if !defined(BOOST_NO_CXX11_VARIADIC_TEMPLATES)  
```  
  
which seems to work for me, but i wonder if it may has some unintended side effects?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-06-14 11:44:27 UTC  
> Url: https://github.com/boostorg/container/issues/245#issuecomment-2167847894  

Many thanks for the suggestion!
