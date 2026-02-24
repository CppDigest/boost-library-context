# #165 - Docca needs to declare dependency on Python interpreter. [Closed]

> Username: grafikrobot  
> Created at: 2024-08-19 18:21:19 UTC  
> Updated at: 2024-08-20 02:24:09 UTC  
> Closed at: 2024-08-20 02:24:09 UTC  
> Url: https://github.com/boostorg/docca/issues/165  

Currently the release build is failing on invoking docca as it doesn't have a configured python interpreter by default. The B2 code, some place, needs to do a `using python ;`.

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-08-20 01:46:52 UTC  
> Url: https://github.com/boostorg/docca/issues/165#issuecomment-2297808133  

That unfortunately can't work exactly like that, as `using` is a project rule, but I extracted the gist of it.
