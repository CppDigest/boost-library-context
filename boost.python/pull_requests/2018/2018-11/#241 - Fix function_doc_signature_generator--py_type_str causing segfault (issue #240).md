# #241 Fix function_doc_signature_generator::py_type_str causing segfault (issue #240) [Closed]

> Username: ricrogz  
> Created at: 2018-11-06 14:04:34 UTC  
> Updated at: 2018-11-26 14:57:58 UTC  
> Closed at: 2018-11-26 14:57:58 UTC  
> Url: https://github.com/boostorg/python/pull/241  

Issue #240 is apparently caused by `s.basename` vanishing before being evaluated, and thus comparing a `nullpointer`.  
  
Storing the return type's name in a `static std::string` prevents the name from vanishing before being checked.

---

## Comment 1

> Username: ricrogz  
> Created_at: 2018-11-26 14:57:58 UTC  
> Url: https://github.com/boostorg/python/pull/241#issuecomment-441667865  

Closing, since the issue is a duplicate of #207, and PR #208 solves it.

---
