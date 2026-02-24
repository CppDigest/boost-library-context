# #62 Avoid dereferencing a nullptr in save_pointer_type::register_type [Merged]

> Username: masterleinad  
> Created at: 2017-09-20 12:42:33 UTC  
> Updated at: 2017-10-05 05:03:13 UTC  
> Merged at: 2017-10-05 05:03:13 UTC  
> Closed at: 2017-10-05 05:03:13 UTC  
> Url: https://github.com/boostorg/serialization/pull/62  

Currently, `save_pointer_type::register_type` possibly dereferences a `nullptr` but uses the value only for type deduction. This causes problems when serializing a `nullptr` with `clang-5`. In particular, the subsequent check (in line 468) is skipped and `save(ar, * t)` (in line 475) is executed.  
  
We can fix this by expecting a pointer type in `register_type` instead of a reference.

---
