# #335 Remove prefix from feature check messages [Merged]

> Username: pdimov  
> Created at: 2020-05-09 13:39:54 UTC  
> Updated at: 2020-07-25 19:32:56 UTC  
> Merged at: 2020-07-25 18:26:44 UTC  
> Closed at: 2020-07-25 18:26:44 UTC  
> Url: https://github.com/boostorg/config/pull/335  

This PR removes the "Boost.Config Feature Check:" prefix from the feature check messages. It's repetitive, takes up horizontal space, and the messages are still clear enough without it.

---

## Comment 1

> Username: pdimov  
> Created_at: 2020-06-03 11:52:01 UTC  
> Url: https://github.com/boostorg/config/pull/335#issuecomment-638147149  

Before/after comparison:  
  
Before:  
```  
Performing configuration checks  
  
    - default address-model    : 32-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : no  (cached)  
    - junctions supported      : yes (cached)  
    - hardlinks supported      : yes (cached)  
    - Boost.Config Feature Check: cxx11_variadic_templates : yes (cached)  
    - Boost.Config Feature Check: cxx11_template_aliases : yes (cached)  
    - Boost.Config Feature Check: cxx11_decltype : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_tuple : yes (cached)  
    - Boost.Config Feature Check: cxx11_variadic_templates : yes (cached)  
    - Boost.Config Feature Check: cxx11_template_aliases : yes (cached)  
    - Boost.Config Feature Check: cxx11_decltype : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_tuple : yes (cached)  
```  
After:  
```  
Performing configuration checks  
  
    - default address-model    : 32-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : no  (cached)  
    - junctions supported      : yes (cached)  
    - hardlinks supported      : yes (cached)  
    - cxx11_variadic_templates : yes (cached)  
    - cxx11_template_aliases   : yes (cached)  
    - cxx11_decltype           : yes (cached)  
    - cxx11_hdr_tuple          : yes (cached)  
    - cxx11_variadic_templates : yes (cached)  
    - cxx11_template_aliases   : yes (cached)  
    - cxx11_decltype           : yes (cached)  
    - cxx11_hdr_tuple          : yes (cached)  
```

---
