# #81 - Standalone build of mp11 with CMake 3.25.1 does not build tests [Closed]

> Username: ilyapopov  
> Created at: 2023-06-23 14:27:22 UTC  
> Updated at: 2023-06-23 14:50:36 UTC  
> Closed at: 2023-06-23 14:50:36 UTC  
> Url: https://github.com/boostorg/mp11/issues/81  

I tried the following:  
  
```  
git clone git@github.com:ilyapopov/mp11.git  
cd mp11  
cmake -B build -DBUILD_TESTING=ON  
make -C build  
make -C build test  
```  
  
and got messages that test executables are not found. I have printed the value of `BUILD_TESTING` just before https://github.com/boostorg/mp11/blob/3e84aef18e2083538bdbb748d63daf8aa7392c7d/CMakeLists.txt#L62 and found it empty. It looks like the line https://github.com/boostorg/mp11/blob/3e84aef18e2083538bdbb748d63daf8aa7392c7d/CMakeLists.txt#L49 does not do what it supposed to.

---

## Comment 1

> Username: ilyapopov  
> Created at: 2023-06-23 14:31:39 UTC  
> Updated at: 2023-06-23 14:35:19 UTC  
> Url: https://github.com/boostorg/mp11/issues/81#issuecomment-1604368959  

Looks like it is related to https://cmake.org/cmake/help/latest/policy/CMP0077.html#policy:CMP0077 , more specifically:  
  
> In CMake 3.13 and above the [option()](https://cmake.org/cmake/help/latest/command/option.html#command:option) command prefers to do nothing when a normal variable of the given name already exists. It does not create or update a cache entry or remove the normal variable. The new behavior is consistent between the first and later runs in a build tree.  
  
So the option command did nothing because there was already a variable provided from the command line, If I understand correctly.   
  
Also may be relevant: https://cmake.org/cmake/help/latest/policy/CMP0126.html

---

## Comment 2

> Username: pdimov  
> Created at: 2023-06-23 14:45:46 UTC  
> Url: https://github.com/boostorg/mp11/issues/81#issuecomment-1604387832  

I believe the reason is that you need to build the `tests` target first instead of the default one.

---

## Comment 3

> Username: ilyapopov  
> Created at: 2023-06-23 14:50:36 UTC  
> Url: https://github.com/boostorg/mp11/issues/81#issuecomment-1604393935  

Oh, yes, that works, sorry for the false alarm.
