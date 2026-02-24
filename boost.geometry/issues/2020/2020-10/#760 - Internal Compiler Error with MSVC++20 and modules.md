# #760 - Internal Compiler Error with MSVC++20 and modules [Closed]

> Username: myblindy  
> Created at: 2020-10-02 18:52:42 UTC  
> Updated at: 2021-05-12 09:04:03 UTC  
> Closed at: 2021-05-12 09:04:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/760  

You can clone [this repository](https://github.com/myblindy/boost_compiler_crash_repro) to reproduce it, I tried to make it as small as possible, it's just a tree query.   
  
If moved outside of the module, the code works correctly. Inside it just dies.   
  
I'm using VS 16.8 Preview 3.1, and I got installed the boost library with `vcpkg install boost-geometry:x64-windows` (the application is 64-bit).

---

## Comment 1

> Username: mloskot  
> Created at: 2020-10-02 18:55:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/760#issuecomment-702902831  

I think this report belongs to https://developercommunity.visualstudio.com

---

## Comment 2

> Username: myblindy  
> Created at: 2020-10-02 19:01:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/760#issuecomment-702905793  

I reported it [there](https://developercommunity.visualstudio.com/content/problem/1207975/internal-compiler-error-with-boost-geometry-tree-q.html) as well, you can close this if you believe C++20 modules shouldn't be supported.

---

## Comment 3

> Username: mloskot  
> Created at: 2020-10-02 20:33:11 UTC  
> Updated at: 2020-10-03 11:32:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/760#issuecomment-702944937  

👍  No, it's not that C++20 modules are not supported deliberately in anyway. It's just that nobody probably has put any effort to ensure Boost.Geometry (or even Boost in general) supports it well.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2021-05-12 09:04:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/760#issuecomment-839601833  

Closing this issue, I don't think we can fix internal compiler errors
