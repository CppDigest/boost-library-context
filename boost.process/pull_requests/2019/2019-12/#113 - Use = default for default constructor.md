# #113 Use = default for default constructor [Merged]

> Username: jonesmz  
> Created at: 2019-12-05 18:41:50 UTC  
> Updated at: 2020-03-23 21:39:18 UTC  
> Merged at: 2020-01-26 08:14:52 UTC  
> Closed at: 2020-01-26 08:14:52 UTC  
> Url: https://github.com/boostorg/process/pull/113  



---

## Comment 1

> Username: SigmundVik  
> Created_at: 2020-02-06 11:53:05 UTC  
> Url: https://github.com/boostorg/process/pull/113#issuecomment-582869926  

Commit `8541cae` causes this compilation error for Visual C++ 14.1 (and 14.2):  
  
`boost\include\boost/process/child.hpp(35): error C2600: 'boost::process::child::child': cannot define a compiler-generated special member function (must be declared in the class first)`  
  
The same can be seen here:  
https://ci.appveyor.com/project/klemens-morgenstern/boost-process/branch/develop

---

## Comment 2

> Username: jonesmz  
> Created_at: 2020-02-06 17:44:37 UTC  
> Url: https://github.com/boostorg/process/pull/113#issuecomment-583024864  

That... Seems like a compiler bug, given its declared and defined in the class only?

---

## Comment 3

> Username: SigmundVik  
> Created_at: 2020-02-06 21:36:46 UTC  
> Url: https://github.com/boostorg/process/pull/113#issuecomment-583122824  

I created a self-contained repro case and posted it to the Visual Studio Developer Community:  
https://developercommunity.visualstudio.com/content/problem/909914/unexpected-compilation-error-for-compiler-generate.html

---

## Comment 4

> Username: Naios  
> Created_at: 2020-03-23 21:37:45 UTC  
> Updated_at: 2020-03-23 21:39:18 UTC  
> Url: https://github.com/boostorg/process/pull/113#issuecomment-602870171  

See #152, I created a workaround by reverting your commit partially since leaving MSVC 14.1 and 14.2 in a non builable state is also not a solution to this issue.

---
