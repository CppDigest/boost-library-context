# #125 - Docs: add tutorial for `limits` [Closed]

> Username: akrzemi1  
> Created at: 2024-01-19 04:40:03 UTC  
> Updated at: 2024-01-22 07:16:40 UTC  
> Closed at: 2024-01-22 07:16:40 UTC  
> Url: https://github.com/boostorg/charconv/issues/125  

Class `limits` has its own public header, and appears to be part of the library's interface. But the docs never say what it is for and when and how users are expected to use it.  
  
It looks like in the safe usages users would use its constants to determine the sizes of their buffers. I think the docs should show such real life example.

---

## Comment 1

> Username: mborland  
> Created at: 2024-01-19 08:27:26 UTC  
> Url: https://github.com/boostorg/charconv/issues/125#issuecomment-1899966821  

Added in https://github.com/cppalliance/charconv/pull/127.
