# #22 Fix gcc -Wdeprecated-copy [Merged]

> Username: stac47  
> Created at: 2019-02-28 16:04:36 UTC  
> Updated at: 2019-02-28 17:25:21 UTC  
> Merged at: 2019-02-28 17:25:21 UTC  
> Closed at: 2019-02-28 17:25:21 UTC  
> Url: https://github.com/boostorg/multi_index/pull/22  

Hello,  
  
This patch fixes:  
  
```  
/path/include/boost/multi_index/detail/ord_index_node.hpp:357:24: error: implicitly-declared 'constexpr boost::multi_index::detail::ordered_index_node_compressed_base<b  
oost::multi_index::detail::null_augment_policy, std::allocator<char> >::parent_ref::parent_ref(const boost::multi_index::detail::ordered_index_node_compressed_base<boost::multi_index::detail::null_augment_policy, std::allocator<char> >::parent_ref&)' is  
deprecated [-Werror=deprecated-copy]  
  357 |             rotate_left(x,root);  
      |             ~~~~~~~~~~~^~~~~~~~  
/path/include/boost/multi_index/detail/ord_index_node.hpp:182:17: note: because 'boost::multi_index::detail::ordered_index_node_compressed_base<boost::multi_index::deta  
il::null_augment_policy, std::allocator<char> >::parent_ref' has user-provided 'boost::multi_index::detail::ordered_index_node_compressed_base<AugmentPolicy, Allocator>::parent_ref& boost::multi_index::detail::ordered_index_node_compressed_base<AugmentPo  
licy, Allocator>::parent_ref::operator=(const boost::multi_index::detail::ordered_index_node_compressed_base<AugmentPolicy, Allocator>::parent_ref&) [with AugmentPolicy = boost::multi_index::detail::null_augment_policy; Allocator = std::allocator<char>]'  
  182 |     parent_ref& operator=(const parent_ref& x)  
      |  
```  
  
Stac

---
