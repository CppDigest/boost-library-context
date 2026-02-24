# #155 - pydocca: overload page for operators related to a class has an incorrect anchor [Closed]

> Username: anarthal  
> Created at: 2024-08-14 12:34:12 UTC  
> Updated at: 2024-08-15 01:56:38 UTC  
> Closed at: 2024-08-15 01:56:37 UTC  
> Url: https://github.com/boostorg/docca/issues/155  

When a class (`field` , in this example) has a bunch of related operators (`operator==`, in this example), linked via `\relates`, and such operators are overloaded, then the generated anchor for the overload set is incorrect.  
  
See attached gist: https://gist.github.com/anarthal/bd11a546fb1e6f0329abea6c900e796b  
  
I've developed a fix (will link a PR), but I don't know enough if your internals to write tests for it.

---

## Comment 1

> Username: anarthal  
> Created at: 2024-08-14 12:54:15 UTC  
> Url: https://github.com/boostorg/docca/issues/155#issuecomment-2288663321  

Edit: gist is now public.
