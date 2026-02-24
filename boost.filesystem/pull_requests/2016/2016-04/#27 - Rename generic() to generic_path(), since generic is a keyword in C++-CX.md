# #27 Rename generic() to generic_path(), since generic is a keyword in C++/CX [Merged]

> Username: fxb  
> Created at: 2016-04-27 12:13:14 UTC  
> Updated at: 2016-04-27 13:23:08 UTC  
> Merged at: 2016-04-27 13:20:39 UTC  
> Closed at: 2016-04-27 13:20:39 UTC  
> Url: https://github.com/boostorg/filesystem/pull/27  

When using Boost.Filesystem from a project compiled as C++/CX code,  
compilation fails with a syntax error, because generic is a keyword.  
  
```  
error C2059: syntax error: 'generic'  
```  
  
See section "Generic interfaces" in C++/CX here: https://msdn.microsoft.com/en-us/library/hh755792.aspx

---

## Comment 1

> Username: fxb  
> Created_at: 2016-04-27 12:15:26 UTC  
> Url: https://github.com/boostorg/filesystem/pull/27#issuecomment-215064192  

@Beman: I'm unsure if renaming this function is considered fine, because it will break the API. Since the comment preceding it says it is experimental, and the function is not included in the documentation yet, I went ahead and created this pull request. Let me know what you think.

---

## Comment 2

> Username: Beman  
> Created_at: 2016-04-27 13:23:08 UTC  
> Url: https://github.com/boostorg/filesystem/pull/27#issuecomment-215081407  

Thanks! Given that we already have a generic_string template, generic_path makes good sense as a name.  
--Beman

---
