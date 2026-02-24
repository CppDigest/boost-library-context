# #237 - Unused variable warnings when compiling nbody example [Closed]

> Username: kylelutz  
> Created at: 2014-08-15 03:38:30 UTC  
> Updated at: 2014-08-20 02:08:19 UTC  
> Closed at: 2014-08-20 02:08:19 UTC  
> Url: https://github.com/boostorg/compute/issues/237  

I'm getting the following warning from clang when compiling `example/nbody.cpp`:  
  
```  
[74/407] Building CXX object example/CMakeFiles/nbody.dir/nbody.cpp.o  
../example/nbody.cpp:183:11: warning: unused variable 'w' [-Wunused-variable]  
    float w = width();  
          ^  
../example/nbody.cpp:184:11: warning: unused variable 'h' [-Wunused-variable]  
    float h = height();  
          ^  
2 warnings generated.  
```  
  
@f-koehler were these intended to be used somewhere or should we just remove them?

---

## Comment 1

> Username: f-koehler  
> Created at: 2014-08-15 06:09:08 UTC  
> Updated at: 2014-08-15 06:09:43 UTC  
> Url: https://github.com/boostorg/compute/issues/237#issuecomment-52277207  

I forgot to remove them. I was experimenting with the transformation of the OpenGL matrix.  
  
I will also add command line arguments using Boost.ProgramOptions

---

## Comment 2

> Username: f-koehler  
> Created at: 2014-08-19 21:33:22 UTC  
> Url: https://github.com/boostorg/compute/issues/237#issuecomment-52702321  

See PR [#241](https://github.com/kylelutz/compute/pull/241).
