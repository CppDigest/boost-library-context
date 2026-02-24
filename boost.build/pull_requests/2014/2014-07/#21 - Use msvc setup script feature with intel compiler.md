# #21 Use msvc setup script feature with intel compiler [Closed]

> Username: bernhard-b  
> Created at: 2014-07-17 18:53:58 UTC  
> Updated at: 2021-10-02 22:35:51 UTC  
> Closed at: 2014-07-25 05:54:16 UTC  
> Url: https://github.com/boostorg/build/pull/21  

It reuses the recently added feature to cache the msvc setup batch files.  
It also fixes the breakage with intel compilerr and msvc 12.0 due new windows store functionality.

---

## Comment 1

> Username: bernhard-b  
> Created_at: 2014-07-20 20:39:42 UTC  
> Url: https://github.com/boostorg/build/pull/21#issuecomment-49558828  

i use a rule resolve-possible-msvc-version-alias in msvc.jam to resolve the alias as msvc normally resolves the correct version in congfigure-really. should be better and future proof now.

---

## Comment 2

> Username: vprus  
> Created_at: 2014-07-25 05:54:16 UTC  
> Url: https://github.com/boostorg/build/pull/21#issuecomment-50110584  

I've merged this, thank you!

---
