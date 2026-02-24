# #1 Re-enable type name demangling [Merged]

> Username: Lastique  
> Created at: 2014-05-31 18:00:01 UTC  
> Updated at: 2014-06-19 07:24:51 UTC  
> Merged at: 2014-06-05 06:29:56 UTC  
> Closed at: 2014-06-05 06:29:56 UTC  
> Url: https://github.com/boostorg/exception/pull/1  

This changeset re-implements type name demangling so that it does not introduce dependency on Boost.Units and still provide improved diagnostic messages to users.

---

## Comment 1

> Username: pdimov  
> Created_at: 2014-06-05 12:46:47 UTC  
> Url: https://github.com/boostorg/exception/pull/1#issuecomment-45214521  

That's pretty useful. Can we move demangle to core?

---

## Comment 2

> Username: Lastique  
> Created_at: 2014-06-05 12:55:10 UTC  
> Url: https://github.com/boostorg/exception/pull/1#issuecomment-45215282  

Yes, that should be possible. I know several other libraries that do demangling themselves.

---
