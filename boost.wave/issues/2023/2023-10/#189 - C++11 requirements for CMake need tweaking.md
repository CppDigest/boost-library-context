# #189 - C++11 requirements for CMake need tweaking [Closed]

> Username: jefftrull  
> Created at: 2023-10-19 20:53:28 UTC  
> Updated at: 2023-10-25 22:32:26 UTC  
> Closed at: 2023-10-25 22:32:26 UTC  
> Url: https://github.com/boostorg/wave/issues/189  

Per @pdimov 's [post](https://lists.boost.org/Archives/boost/2023/10/255042.php) on boost-devel, there are a few requirements for CMake that I didn't update correctly when we began requiring C++11. In particular:  
  
- `cmake_minimum_required` needs to be at least 3.8  
- there is no `target_compile_features` specifying C++11  
- the dependency on `Boost.format` is not reflected  
  
Generally speaking, the CMake build flow is a parallel one that needs to be maintained as we change the bjam stuff.

---

## Comment 1

> Username: pdimov  
> Created at: 2023-10-19 22:46:31 UTC  
> Url: https://github.com/boostorg/wave/issues/189#issuecomment-1771805877  

You can just rerun `boostdep --cmake wave`, but the output isn't 100% correct because you need two more source files  
```  
  src/cpplexer/re2clex/aq.cpp  
  src/cpplexer/re2clex/cpp_re.cpp  
```  
that `boostdep` doesn't pick up because they're in a subdirectory.
