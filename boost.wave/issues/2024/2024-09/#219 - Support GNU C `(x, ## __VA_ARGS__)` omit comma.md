# #219 - Support GNU C `(x, ## __VA_ARGS__)` omit comma. [Open]

> Username: jwnhy  
> Created at: 2024-09-10 14:51:44 UTC  
> Updated at: 2024-10-03 04:26:53 UTC  
> Url: https://github.com/boostorg/wave/issues/219  

GNU C supports a non-standard feature that allows one to omit the variadics without the additional comma.  
  
For example,  
  
```C  
#define pr_fmt(fmt, ...) printf(fmt, __VA_ARGS__)  
```  
  
`pr_fmt("HELLO")` will expand to `printf("HELLO", )` with an additional comma `,`, which is not ideal.  
  
GNU C introduces the feature that if adds a preceding `##`, the additional comma will automatically be removed.  
```C  
#define pr_fmt(fmt, ...) printf(fmt, ## __VA_ARGS__)  
```  
This produces `printf("HELLO")`.  
  
Given that there is already some implementation in C++ 2a around ommitable variadics [here](https://github.com/boostorg/wave/blob/7ce8b53d9dc56a9c0b9a8855f26003e6b174301f/include/boost/wave/util/cpp_macromap.hpp#L1303), it shouldn't be too hard to support this feature.  
  
Linux kernel extensively uses this feature.

---

## Comment 1

> Username: jefftrull  
> Created at: 2024-10-03 04:26:52 UTC  
> Url: https://github.com/boostorg/wave/issues/219#issuecomment-2390490384  

This looks interesting and useful for handling the Linux kernel, an important target for analysis. We should be sure to guard/enable it appropriately as a GNU extension.
