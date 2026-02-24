# #69 - How to specify multiple Python versions to build Boost.Python [Open]

> Username: yurybura  
> Created at: 2024-07-15 11:45:56 UTC  
> Updated at: 2024-07-15 18:17:16 UTC  
> Url: https://github.com/boostorg/cmake/issues/69  

Is there an alternative to specifying and building multiple binaries for a Boost.Python project?  
For example, `boost-python310` and `boost-python312` are not compatible. How to build both binaries using CMake?  
  
B2 provides the following solution: https://www.boost.org/doc/libs/1_85_0/tools/build/doc/html/index.html#bbv2.reference.tools.libraries.python.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-07-15 12:20:23 UTC  
> Url: https://github.com/boostorg/cmake/issues/69#issuecomment-2228372384  

You should be able to set the `Python_ROOT_DIR` variable to point at the desired version.

---

## Comment 2

> Username: yurybura  
> Created at: 2024-07-15 14:20:30 UTC  
> Updated at: 2024-07-15 14:24:08 UTC  
> Url: https://github.com/boostorg/cmake/issues/69#issuecomment-2228623441  

> You should be able to set the `Python_ROOT_DIR` variable to point at the desired version.  
  
With the B2 tool, you can specify multiple versions of Python in a single build using the `python=3.10,3.12` command line option. B2 will create both libraries.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-07-15 17:18:05 UTC  
> Url: https://github.com/boostorg/cmake/issues/69#issuecomment-2229010468  

I don't think this is possible with CMake. It doesn't support building the same target several times with different configurations like b2 does.

---

## Comment 4

> Username: yurybura  
> Created at: 2024-07-15 18:17:15 UTC  
> Url: https://github.com/boostorg/cmake/issues/69#issuecomment-2229109573  

> I don't think this is possible with CMake. It doesn't support building the same target several times with different configurations like b2 does.  
  
Many projects do this with CMake using `ExternalProject_Add`.
