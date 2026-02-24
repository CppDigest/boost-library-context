# #11 - Explain the relationship between op==(T, T) and op==(C, C) w.r.t. container_view [Closed]

> Username: tzlaine  
> Created at: 2019-12-19 16:08:22 UTC  
> Updated at: 2020-01-09 08:18:48 UTC  
> Closed at: 2020-01-09 08:18:48 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/11  

From the Boost review:  
  
"  
Fortunately, that's only a doc problem.  For a container C with value_type T, if operator==(T, T) is defined, then operator==(C, C) is defined.  Similarly, if operator<(T, T) is defined, you get operator<(C, C) from that.  
  
If you provide a custom operator<(C, C), or use the one defined via operator<(T, T) -> operator<(C, C), you get all the other relational operators for C.  This was communicated poorly.  I'll fix that.  
"

---

## Comment 1

> Username: tzlaine  
> Created at: 2020-01-01 19:33:47 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/11#issuecomment-570077627  

Mistakenly closed by putting the wrong issue number in a commit message.
