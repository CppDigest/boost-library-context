# #98 - getting required size for `safe_dump_to` call [Closed]

> Username: Spongman  
> Created at: 2020-08-04 21:19:27 UTC  
> Updated at: 2020-12-15 13:49:55 UTC  
> Closed at: 2020-12-15 13:49:55 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/98  

how is one supposed to query for the required buffer size for a call to `safe_dump_to(void*,size_t)` ? the only way i can think of to do this right now is to keep calling it repeatedly with an increasing buffer size until the return value stops changing.  
  
please don't say 'just choose some random constant value'...

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-12-15 13:45:44 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/98#issuecomment-745295627  

Yep, there's no way to do that.  
  
Compilers can inline, split or not inline functions. Compilers treat `inline`, `force_inline` and `no_inline` as hints and sometimes ignore them. There's no nice way to sync `get_stacktrace_depth()` and `safe_dump_to`.  
  
  
I'm planning to less advertise `safe_dump_to` in next revisions of stacktrace library.
