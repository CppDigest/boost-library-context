# #174 whitespace changes, just test the CI for success (please ignore) [Closed]

> Username: d5ch4k  
> Created at: 2024-05-04 20:15:19 UTC  
> Updated at: 2024-10-01 19:05:05 UTC  
> Closed at: 2024-09-30 17:45:09 UTC  
> Url: https://github.com/boostorg/parser/pull/174  

I need to be sure, that my changes broke the build (please ignore)  
I hope theses subtle changes will trigger the github actions ci

---

## Comment 1

> Username: d5ch4k  
> Created_at: 2024-05-04 22:35:26 UTC  
> Updated_at: 2024-05-05 22:40:03 UTC  
> Url: https://github.com/boostorg/parser/pull/174#issuecomment-2094489920  

if you add some verbosity to the fedora.yml file by adding `-DCMAKE_VERBOSE_MAKEFILE=ON`  
i.e.  
```  
          cmake -B build -G Ninja \  
           -DCMAKE_VERBOSE_MAKEFILE=ON \  
           -DCMAKE_BUILD_TYPE=${{ env.BUILD_TYPE }} \  
           -DCMAKE_C_COMPILER="${CC}" \  
           -DCMAKE_CXX_COMPILER="${CXX}" \  
           -DCXX_STD=${{ matrix.cxx_std }}  
```  
  
then ninja will be called with -v and you can see in **include/boost/parser/detail/text/transcode_view.hpp** a lot of   
`error: friend function template with constraints that depend on outer template parameters must be a definition` now with the new gcc-14.0.1  
  
The error in the Windows build **[build (23, windows-2022)]** has an issue with the pipe operator for a `char const *` type string  
```  
        char const * str_ = "XYZ";  
        auto str = str_ | bp::as_utf8;  
```

---

## Comment 2

> Username: d5ch4k  
> Created_at: 2024-05-04 22:40:21 UTC  
> Url: https://github.com/boostorg/parser/pull/174#issuecomment-2094492163  

I just learned that issue #169 is related

---
