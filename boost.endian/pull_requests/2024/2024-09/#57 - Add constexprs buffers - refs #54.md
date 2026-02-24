# #57 Add constexprs buffers - refs #54 [Open]

> Username: Julien-Blanc-tgcm  
> Created at: 2024-09-09 12:13:23 UTC  
> Updated at: 2024-09-11 05:50:21 UTC  
> Url: https://github.com/boostorg/endian/pull/57  

* allows several operations on buffer types to be constexpr  
* ensure old execution path is still used at runtime, to avoid any performance regression

---
