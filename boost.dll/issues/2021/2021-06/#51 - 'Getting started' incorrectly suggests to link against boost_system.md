# #51 - 'Getting started' incorrectly suggests to link against boost_system [Closed]

> Username: reavertm  
> Created at: 2021-06-09 16:07:03 UTC  
> Updated at: 2025-01-14 18:47:07 UTC  
> Closed at: 2025-01-14 18:47:07 UTC  
> Url: https://github.com/boostorg/dll/issues/51  

'Getting started' page says:  
  
> 'Importing code requires linking with boost_filesystem and boost_system libraries.  
  
but libdl.so is not in NEEDED section of libboost_system.so, which obviously results with linking error:  
  
> /usr/bin/c++ -g -rdynamic CMakeFiles/common-logic.dir/main.cpp.o -o common-logic  -lboost_filesystem -lboost_system   
> /usr/bin/ld: CMakeFiles/common-logic.dir/main.cpp.o: in function `boost::dll::detail::reset_dlerror()':  
> /usr/include/boost/dll/detail/system_error.hpp:27: undefined reference to `dlerror'  
  
(and for all other functions: dlopen, dlsym etc)  
  
Please either update guideline to inform users to link their relevant OS library themselves, or maybe add said OS library (libdl.so, other OS equivalent) as library dependency for boost_system so that it's pulled automatically.
