# #494 - Including the Multiprecision header file causing a compilation error related to left shifting a negative number [Closed]

> Username: GregorySchwing  
> Created at: 2022-09-15 00:21:32 UTC  
> Updated at: 2022-09-15 15:54:41 UTC  
> Closed at: 2022-09-15 15:54:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/494  

I can compile a simple file with g++ with no problem.  
  
[bigtrits.cpp.txt](https://github.com/boostorg/multiprecision/files/9570755/bigtrits.cpp.txt)  
  
g++ bigtrits.cpp  
  
When I move the code into a header file and compile in a larger project,  
  
[TritArrayMaker.h.txt](https://github.com/boostorg/multiprecision/files/9570756/TritArrayMaker.h.txt)  
  
Full error:  
/usr/include/boost/multiprecision/cpp_int/cpp_int_config.hpp:94:164: error: left operand of shift expression ‘(-9223372036854775808 << 64)’ is negative [-fpermissive]  
   94 | class integer_traits<multiprecision::signed_double_limb_type>  
      |                                                                                                                                                                    ^     
/usr/include/boost/multiprecision/cpp_int/cpp_int_config.hpp:94:169: note: in template argument for type ‘__int128’  
   94 | class integer_traits<multiprecision::signed_double_limb_type>  
  
Compilation details:  
  
-- The CUDA compiler identification is NVIDIA 11.7.99  
-- The CXX compiler identification is GNU 11.2.0  
-- Detecting CUDA compiler ABI info  
-- Detecting CUDA compiler ABI info - done  
-- Check for working CUDA compiler: /usr/local/cuda/bin/nvcc - skipped  
-- Detecting CUDA compile features  
-- Detecting CUDA compile features - done  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: /usr/bin/c++ - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Looking for C++ include pthread.h  
-- Looking for C++ include pthread.h - found  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Success  
-- Found Threads: TRUE    
-- Found CUDA: /usr/local/cuda (found version "11.7")   
-- CUDA_INCLUDE_DIR:   
-- CUDA_INCLUDE_DIRS	: /usr/local/cuda/include  
-- CUDA_TOOLKIT_ROOT_DIR: /usr/local/cuda  
-- CUDA_LIBRARY:   
-- CUDA_LIBRARY: /usr/local/cuda/lib64/libcudart_static.a;Threads::Threads;dl;/usr/lib/x86_64-linux-gnu/librt.a  
-- Found TBB: /usr/include (found version "2021.5")    
-- TBB_INCLUDE_DIR:   
-- TBB_INCLUDE_DIRS: /usr/include  
-- TBB_ROOT_DIR:   
-- TBB::tbb: ${TBB::tbb}  
-- TBB_LIBRARIES: /usr/lib/x86_64-linux-gnu/libtbb.so  
-- TBB_LIBRARY:   
-- Found Curses: /usr/lib/x86_64-linux-gnu/libcurses.so    
-- CURSES_INCLUDE_DIR: /usr/include  
-- CURSES_LIBRARIES: /usr/lib/x86_64-linux-gnu/libcurses.so;/usr/lib/x86_64-linux-gnu/libform.so  
-- CURSES_LIBRARY: /usr/lib/x86_64-linux-gnu/libcurses.so  
-- Found Boost: /usr/lib/x86_64-linux-gnu/cmake/Boost-1.74.0/BoostConfig.cmake (found version "1.74.0") found components: iostreams program_options   
-- Configuring done  
-- Generating done  
-- Build files have been written to: /home/greg/Documents/Multicore-Matching/build

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-09-15 09:55:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/494#issuecomment-1247869244  

That section of problem code has been removed from more recent Boost releases, can you update your Boost version?

---

## Comment 2

> Username: GregorySchwing  
> Created at: 2022-09-15 15:54:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/494#issuecomment-1248294179  

Sure.  Thanks.
