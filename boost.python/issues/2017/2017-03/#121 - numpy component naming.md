# #121 - numpy component naming [Closed]

> Username: rleigh-codelibre  
> Created at: 2017-03-25 22:46:25 UTC  
> Updated at: 2017-03-25 23:44:04 UTC  
> Closed at: 2017-03-25 23:44:04 UTC  
> Url: https://github.com/boostorg/python/issues/121  

Hi,  
  
I've been prepping the CMake support for 1.64, including numpy support: https://gitlab.kitware.com/rleigh/cmake/commits/boost-1.64-deps  
  
While we have `python` and `python_mpi` components matching the corresponding `boost_python` and `boost_python_mpi` libraries, `numpy` isn't named `python_numpy` which is a little inconsistent with the existing naming.  Since we're going to be using this as a CMake package component name which has compatibility concerns, I wanted to double check if this was deliberate or an oversight, and what the rationale was?  
  
Many thanks,  
Roger

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-03-25 22:56:02 UTC  
> Url: https://github.com/boostorg/python/issues/121#issuecomment-289245293  

I think ideally I would have called the project Boost.Numpy, but that would require for it to be a separate project (requiring a formal review process etc., when it technically is just an extension to Boost.Python.)  
  
But since the term "NumPy" already contains "Python", I found "boost_python_numpy" to be redundant, so I shortened it to "boost_numpy".

---

## Comment 2

> Username: rleigh-codelibre  
> Created at: 2017-03-25 23:44:04 UTC  
> Url: https://github.com/boostorg/python/issues/121#issuecomment-289247484  

OK, thanks for the clarification.  I'll leave things as they are on the CMake side in this case, using `numpy` as the Boost sub-component name.
