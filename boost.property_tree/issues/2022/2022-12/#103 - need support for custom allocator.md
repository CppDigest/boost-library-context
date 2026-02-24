# #103 - need support for custom allocator [Open]

> Username: Batodalaev  
> Created at: 2022-12-06 07:02:01 UTC  
> Updated at: 2024-11-01 20:18:58 UTC  
> Url: https://github.com/boostorg/property_tree/issues/103  

multi_index_container provide typename Allocator, but basic_ptree doesn't provide it.   
How i can use custom allocator for basic_ptree?   
  
I already use custom allocator for key and values.

---

## Comment 1

> Username: Batodalaev  
> Created at: 2022-12-06 07:08:15 UTC  
> Url: https://github.com/boostorg/property_tree/issues/103#issuecomment-1338883747  

multi_index_container use by default std::allocator

---

## Comment 2

> Username: Xxproner  
> Created at: 2024-11-01 20:18:58 UTC  
> Url: https://github.com/boostorg/property_tree/issues/103#issuecomment-2452531870  

+1
