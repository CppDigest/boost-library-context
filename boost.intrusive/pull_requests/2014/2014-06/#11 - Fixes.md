# #11 Fixes [Closed]

> Username: mateidavid  
> Created at: 2014-06-13 01:28:12 UTC  
> Updated at: 2014-08-14 16:20:28 UTC  
> Closed at: 2014-06-27 07:01:28 UTC  
> Url: https://github.com/boostorg/intrusive/pull/11  

Use `reserve()` to make `boost::container::vector` work with `bounded_reference`.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2014-06-27 07:01:28 UTC  
> Url: https://github.com/boostorg/intrusive/pull/11#issuecomment-47313632  

Thanks for the patch. This pull request is not needed now as Boost.Container in master/develop is now ready for types (like bounded_reference) with overloaded "operator &".

---
