# #109 - Cache size detection [Closed]

> Username: mborland  
> Created at: 2020-09-28 15:54:03 UTC  
> Updated at: 2020-10-21 00:53:54 UTC  
> Closed at: 2020-10-02 23:16:50 UTC  
> Url: https://github.com/boostorg/predef/issues/109  

A recurring discussion over in boost.math has been the need to query the L1D cache size of a machine. [I have a repo](https://github.com/mborland/cache) (windows functionality has not been validated yet) that should solve this. I am looking for a home for it in boost because it would be useful outside of just math. Do you think it makes sense to add here? I can move it into a PR if so.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-10-21 00:53:54 UTC  
> Url: https://github.com/boostorg/predef/issues/109#issuecomment-713223703  

I see you closed this.. But to answer your question anyway..  
  
The goal of Predef is to provide "compile time" introspection as to the compilation context. Since cache topology can only be a runtime aspect it's not suited to Predef.
