# #78 - state of the output argument upon unsuccessful parse [Closed]

> Username: akrzemi1  
> Created at: 2024-01-27 08:08:20 UTC  
> Updated at: 2024-01-28 22:45:40 UTC  
> Closed at: 2024-01-28 22:45:40 UTC  
> Url: https://github.com/boostorg/parser/issues/78  

The docs should document that upon unsuccessful parse the value of the output parameter (where the result is supposed to be written) is in an unspecified state (potentially changed).  
  
An example illustrating this:  
https://godbolt.org/z/a5vb8E87o

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-01-27 22:16:55 UTC  
> Url: https://github.com/boostorg/parser/issues/78#issuecomment-1913351768  

Will do.

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-01-28 04:21:13 UTC  
> Url: https://github.com/boostorg/parser/issues/78#issuecomment-1913443486  

I rethought this.  Instead of just documenting the behavior, I made the `parse()` API clear the out-param on failure (via `attr = Attr()`).  This is more consistent with what parsers do; when they fail, they do `attr = Attr()`.
