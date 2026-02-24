# #130 - include/boost/iostreams/device/mapped_file.hpp 349 visibility problems [Open]

> Username: incardon  
> Created at: 2021-04-05 20:05:55 UTC  
> Updated at: 2021-04-05 20:05:55 UTC  
> Url: https://github.com/boostorg/iostreams/issues/130  

For time reason I cound not do more testing, but in boost 1.75.0, look like there is a conflict in visibility.  
  
/home/i-bird/openfpm_dependencies_clang/BOOST/include/boost/iostreams/device/mapped_file.hpp:349:53: error: 'safe_bool_helper' is a private member of 'boost::iostreams::mapped_file_source'  
using mapped_file::operator int mapped_file_source::safe_bool_helper::*;  
                                                    ^  
/home/i-bird/openfpm_dependencies_clang/BOOST/include/boost/iostreams/device/mapped_file.hpp:155:8: note: implicitly declared private here  
struct safe_bool_helper { int x; };   
  
... Looking at the code as reported  by the compiler ... I have the tendency to give right to the compiler. On another note I have to admit I compiled BOOST with many compilers gcc nvcc clang and this is the first time I saw this error. It happen with the combo nvcc 11.2 + clang with underlying compiler    
  
The warkaround was to move  the line under  
typedef int safe_bool_helper::*                 safe_bool;  
  
into the public section
