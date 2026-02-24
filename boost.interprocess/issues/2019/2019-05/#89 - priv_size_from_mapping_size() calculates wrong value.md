# #89 - priv_size_from_mapping_size() calculates wrong value [Closed]

> Username: nikego  
> Created at: 2019-05-22 16:55:46 UTC  
> Updated at: 2020-06-02 22:39:44 UTC  
> Closed at: 2020-06-02 22:39:44 UTC  
> Url: https://github.com/boostorg/interprocess/issues/89  

boost 1.70  
Windows 7 x64  
VS2015  
  
When I try to create mapped_region only with 'offset' argument, the function priv_size_from_mapping_size() returns the wrong size of a tail that causes crash in map_view_of_file_ex () and throw of an exception.  
  
The size is calculated as: size = mapping_size - (offset - page_offset)  
  
The function map_view_of_file_ex() receives address (offset - page_offset) and the size (page_offset + size). Their sum is  (size + offset). It is obvious that if the 'page_offset' is not zero, then the sum exceeds 'mapping_size'. It causes access violation in the low-level MapViewOfFile() function.  
  
```diff --git a/mapped_region.hpp "b/mapped_region.hpp"  
index 1fb6408..5c58cb6 100644  
--- a/mapped_region.hpp  
+++ "b/mapped_region.hpp"  
@@ -353,7 +353,7 @@ inline void mapped_region::priv_size_from_mapping_size  
       error_info err(size_error);  
       throw interprocess_exception(err);  
    }  
-   size = static_cast<std::size_t>(mapping_size - (offset - page_offset));  
+   size = static_cast<std::size_t>(mapping_size - offset);  
 }  
   
 inline offset_t mapped_region::priv_page_offset_addr_fixup(offset_t offset, const void *&address)  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-06-02 22:39:44 UTC  
> Url: https://github.com/boostorg/interprocess/issues/89#issuecomment-637844869  

Many thanks for the report. Better late than never.
