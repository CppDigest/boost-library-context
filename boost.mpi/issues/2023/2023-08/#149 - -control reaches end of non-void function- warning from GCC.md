# #149 - "control reaches end of non-void function" warning from GCC [Closed]

> Username: iv-m  
> Created at: 2023-08-15 09:24:38 UTC  
> Updated at: 2023-09-18 14:13:22 UTC  
> Closed at: 2023-09-18 14:13:22 UTC  
> Url: https://github.com/boostorg/mpi/issues/149  

boost 1.83.0 does not build with gcc 13.2 because compilation of `libs/mpi/src/environment.cpp` fails with the following diagnostics:  
  
```  
    "g++"  -fPIC -m64 -pthread -fvisibility=default -pipe -frecord-gcc-switches -Wall -g -O2 -flto=auto -ffat-lto-objects -fno-strict-aliasing  -DBOOST_ALL_NO_LIB=1 -DBOOST_MPI_DYN_LINK=1 -DBOOST_MPI_SOURCE=1 -DNDEBUG   -I"." -I"/usr/lib64/  
openmpi/include"  -c -o "bin.v2/libs/mpi/build/gcc-13/release/local-visibility-global/optimization-off/pch-off/threading-multi/visibility-hidden/environment.o" "libs/mpi/src/environment.cpp"                                                    
                                                                                                                                                                                                                                                  
libs/mpi/src/environment.cpp: In function 'int boost::mpi::detail::level2int(boost::mpi::threading::level)':                                                                                                                                      
libs/mpi/src/environment.cpp:35:23: error: control reaches end of non-void function [-Werror=return-type]                                                                                                                                         
   35 |     environment::abort(70); // EX_SOFTWARE                                                                                                                                                                                                
      |     ~~~~~~~~~~~~~~~~~~^~~~                                                                                                                                                                                                                
libs/mpi/src/environment.cpp: In function 'boost::mpi::threading::level boost::mpi::detail::int2level(int)':                                                                                                                                      
libs/mpi/src/environment.cpp:53:23: error: control reaches end of non-void function [-Werror=return-type]                                                                                                                                         
   53 |     environment::abort(70); // EX_SOFTWARE                                                                                                                                                                                                
      |     ~~~~~~~~~~~~~~~~~~^~~~                                                                                                                                                                                                                
cc1plus: some warnings being treated as errors                                                                                                                                                                                                    
```

---

## Comment 1

> Username: iv-m  
> Created at: 2023-08-15 09:26:50 UTC  
> Url: https://github.com/boostorg/mpi/issues/149#issuecomment-1678651755  

It seems that `environment::abort` should me marked as `[[noreturn]]`, but I'm not sure that this would be a valid statement and how exactly this should be done in a portable way.
