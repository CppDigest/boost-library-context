# #189 - Unable to use geometry with quantities [Closed]

> Username: fDarkShadow  
> Created at: 2021-05-25 12:32:16 UTC  
> Updated at: 2021-05-25 12:52:03 UTC  
> Closed at: 2021-05-25 12:52:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/189  

Hi,  
  
I tried to use quantities (angles) in geometry shapes, so I can use geometry algorithms. But in some source code, an int is declared to compute area, so it can't be used with quantities.  
  
An example is on Coliru : [http://coliru.stacked-crooked.com/a/2b97d1e12d37f764](url).  
  
Tried also with boost 1.76.0, problem still present (in a different file).

---

## Comment 1

> Username: Lastique  
> Created at: 2021-05-25 12:52:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/189#issuecomment-847842437  

This has nothing to do with Boost.Filesystem. Try reporting this to [Boost.Geometry](https://github.com/boostorg/geometry/issues) or [Boost.Units](https://github.com/boostorg/units/issues).
