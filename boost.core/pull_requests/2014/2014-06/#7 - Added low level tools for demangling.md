# #7 Added low level tools for demangling. [Merged]

> Username: Lastique  
> Created at: 2014-06-12 15:27:48 UTC  
> Updated at: 2014-06-12 17:27:55 UTC  
> Merged at: 2014-06-12 16:05:17 UTC  
> Closed at: 2014-06-12 16:05:17 UTC  
> Url: https://github.com/boostorg/core/pull/7  

The commit adds demangle_alloc()/demangle_free() and scoped_demangled_name helper scope guard to provide low level, yet portable interface for symbol demangling. Requested in https://github.com/boostorg/type_index/pull/4.

---

## Comment 1

> Username: pdimov  
> Created_at: 2014-06-12 15:58:29 UTC  
> Url: https://github.com/boostorg/core/pull/7#issuecomment-45911039  

Looks good to me.

---

## Comment 2

> Username: pdimov  
> Created_at: 2014-06-12 15:59:22 UTC  
> Url: https://github.com/boostorg/core/pull/7#issuecomment-45911189  

A purely stylistic question: why do you prefer indenting the code examples in Quickbook instead of using double backquote? Is there a difference between the two, or is it just a stylistic preference?

---

## Comment 3

> Username: Lastique  
> Created_at: 2014-06-12 16:04:39 UTC  
> Url: https://github.com/boostorg/core/pull/7#issuecomment-45911921  

For most use cases there is no difference. I think, double backquote may help in more complicated contexts, like code block inside a table cell. It's just the code samples are more apparent this way, and it looks closer to what it will look like in the generated html.

---
