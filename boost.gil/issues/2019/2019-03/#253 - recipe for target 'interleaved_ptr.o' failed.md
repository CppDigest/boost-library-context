# #253 - recipe for target 'interleaved_ptr.o' failed [Closed]

> Username: sid19991  
> Created at: 2019-03-06 17:45:17 UTC  
> Updated at: 2019-03-06 18:11:40 UTC  
> Closed at: 2019-03-06 18:11:40 UTC  
> Url: https://github.com/boostorg/gil/issues/253  

I'm building the examples with makefile provided in the folder but it is unable to build the interleaved.o giving this error.  
  
recipe for target 'interleaved_ptr.o' failed

---

## Comment 1

> Username: mloskot  
> Created at: 2019-03-06 18:11:40 UTC  
> Url: https://github.com/boostorg/gil/issues/253#issuecomment-470214880  

FYI, https://github.com/boostorg/gil/commit/9004a676625a48aea96e86b31b9dd4198a779fd9  
```  
Remove old example/Makefile  
  
It is not maintained; may be broken; redundant.  
```  
  
Please, use Boost.Build with provided `Jamfile` or CMake with provided `CMakeLists.txt` or a build configuration for your IDE.  
  
For Boost.Build or CMake, learn more from https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md
