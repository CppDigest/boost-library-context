# #279 - Wanted: Workaround for deduction guide detection on gcc [Closed]

> Username: HDembinski  
> Created at: 2019-04-19 15:42:47 UTC  
> Updated at: 2019-04-24 10:44:36 UTC  
> Closed at: 2019-04-21 08:17:49 UTC  
> Url: https://github.com/boostorg/config/issues/279  

gcc-7 and gcc-8 both fail the deduction guide detection check.  
`gcc-7 (Ubuntu 7.3.0-27ubuntu1~18.04) 7.3.0`  
`gcc-8 (Ubuntu 8.2.0-1ubuntu2~18.04) 8.2.0`  
  
```  
g++-7 -std=c++17 ../config/checks/std/cpp_deduction_guides_17.cpp   
../config/checks/std/cpp_deduction_guides_17.cpp:23:2: error: #error "Macro __cpp_deduction_guides had too low a value"  
 #error "Macro __cpp_deduction_guides had too low a value"  
```  
  
```  
g++-8 -std=c++17 ../config/checks/std/cpp_deduction_guides_17.cpp   
../config/checks/std/cpp_deduction_guides_17.cpp:23:2: error: #error "Macro __cpp_deduction_guides had too low a value"  
 #error "Macro __cpp_deduction_guides had too low a value"  
```  
  
These compilers support deduction guides, but the value for __cpp_deduction_guides is 201606 or 201611, respectively.  
https://gcc.gnu.org/projects/cxx-status.html#cxx17  
  
Perhaps the threshold value can be lowered to 201611?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-04-21 08:18:33 UTC  
> Url: https://github.com/boostorg/config/issues/279#issuecomment-485234000  

Done, I'm a bit concerned that there are corner cases that the lower threshold won't handle, but we'll see.

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-04-24 10:44:36 UTC  
> Url: https://github.com/boostorg/config/issues/279#issuecomment-486171418  

I hope it is going to be ok, thank you for merging.
