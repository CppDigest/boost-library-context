# #176 Fix compilation of subchroma_image.hpp as self-contained headers [Merged]

> Username: mloskot  
> Created at: 2018-12-06 22:57:01 UTC  
> Updated at: 2018-12-07 15:00:51 UTC  
> Merged at: 2018-12-07 15:00:17 UTC  
> Closed at: 2018-12-07 15:00:18 UTC  
> Url: https://github.com/boostorg/gil/pull/176  

Rename `Scaling_Factors` to lower-case, as well as its template parameters.  
Move `scaling_factors` to namespace `detail` and before it is use.  
Fix access to `scaling_factors` members in derived classes.  
Add missing `typename`.  
Remove superfluous thus incorrect `typename`.  
Remove `subchroma_image_view` implicit friends with self  
- Fixes template parameter aliasing for Locator and Factors.  
  
Subset of fixes from pending PR #164  
  
### References  
  
- #164  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2018-12-06 23:56:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/176#pullrequestreview-182503438  

📁 include/boost/gil/extension/toolbox/image_types/subchroma_image.hpp

```diff
  66 |+                          );
  67 |+ };
  68 |+ 
```

> Username: stefanseefeld  
> Created_at: 2018-12-06 23:56:41 UTC  
> Updated_at: 2018-12-07 09:13:42 UTC  
> Url: https://github.com/boostorg/gil/pull/176#discussion_r239657930  

It looks like this is only used as a helper type inside the class below. I would therefore suggest to move this into a `detail` namespace to "hide" it from the public API.

> Username: mloskot  
> Created_at: 2018-12-07 09:16:06 UTC  
> Url: https://github.com/boostorg/gil/pull/176#discussion_r239738934  

Good idea.   
  
I have also renamed `Scaling_Factors` to lower-case, as well as its template parameters.  
  
The header test passes now:  
  
```  
mloskot@bionic:/mnt/d/boost.wsl/libs/gil$ colorbb -j 8 -q test/headers//extension/toolbox  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
...patience...  
...found 1801 targets...  
...updating 153 targets...  
...  
gcc.compile.c++ ../../bin.v2/libs/gil/test/headers/h-extension-toolbox-image_types-subchroma_image.test/gcc-5.5.0/debug/visibility-hidden/h-extension-toolbox-image_types-subchroma_image.o  
**passed** ../../bin.v2/libs/gil/test/headers/h-extension-toolbox-image_types-subchroma_image.test/gcc-5.5.0/debug/visibility-hidden/h-extension-toolbox-image_types-subchroma_image.test  
```


---

## Comment 2

> Username: mloskot  
> Created_at: 2018-12-07 14:52:10 UTC  
> Url: https://github.com/boostorg/gil/pull/176#issuecomment-445255835  

@stefanseefeld I think it's ready. Merge?

---
