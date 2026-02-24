# #25 Use consistent naming scheme for python subdir in LIBDIR [Merged]

> Username: SoapGentoo  
> Created at: 2019-12-23 10:06:10 UTC  
> Updated at: 2020-01-07 13:27:49 UTC  
> Merged at: 2019-12-23 14:22:53 UTC  
> Closed at: 2019-12-23 14:22:53 UTC  
> Url: https://github.com/boostorg/boost_install/pull/25  

Using `$LIBDIR/boost-pythonX.Y` is preferable to `$LIBDIR/boost-pyX.Y` as boost uses `python` as a consistent prefix/suffix for python specific libraries.  
@pdimov

---

## Comment 1

> Username: pdimov  
> Created_at: 2020-01-07 13:06:51 UTC  
> Url: https://github.com/boostorg/boost_install/pull/25#issuecomment-571578108  

I've merged this PR for looking obviously correct, but on second look, are you sure it does what you wanted?  
  
The place it changes -py to -python does not affect the subdirectory, but the name mangling of the .cmake files, as far as I can see.

---

## Comment 2

> Username: SoapGentoo  
> Created_at: 2020-01-07 13:14:30 UTC  
> Url: https://github.com/boostorg/boost_install/pull/25#issuecomment-571580630  

@pdimov oh sorry, I've just pattern matched your PR and tried to make sense of it. I don't really grok the bjam syntax, hence I wasn't aware. What do I need to change to just affect the naming of the subdirectory?

---

## Comment 3

> Username: pdimov  
> Created_at: 2020-01-07 13:27:49 UTC  
> Url: https://github.com/boostorg/boost_install/pull/25#issuecomment-571585071  

I'll fix it.

---
