# #182 - Hana: Error linking test.foldable.iterable_mcd.iterable [Closed]

> Username: Syntaf  
> Created at: 2015-09-24 21:36:30 UTC  
> Updated at: 2015-09-24 21:58:06 UTC  
> Closed at: 2015-09-24 21:50:30 UTC  
> Url: https://github.com/boostorg/hana/issues/182  

I'm having an error I cannot figure out when attempting to build the library.  
  
I downloaded the latest release from the github release tag, and believe I have the correct prerequisites: my clang version is 3.5.0 and I have libc++ installed.  
  
After running Cmake without any trouble, using `make` will give me an error upon attempting to link `test.foldable.iterable_mcd.iterable`. The specific error is:  
  
```  
Linking CXX executable test.foldable.iterable_mcd.iterable  
CMakeFiles/test.foldable.iterable_mcd.iterable.dir/foldable/iterable_mcd/iterable.cpp.o: In function `std::__1::vector<int, std::__1::allocator<int> >::allocate(unsigned long)':  
/home/gdev/Downloads/hana-0.4.0/test/foldable/iterable_mcd/iterable.cpp:(.text._ZNSt3__16vectorIiNS_9allocatorIiEEE8allocateEm[_ZNSt3__16vectorIiNS_9allocatorIiEEE8allocateEm]+0x40): undefined reference to `std::__1::__vector_base_common<true>::__throw_length_error() const'  
CMakeFiles/test.foldable.iterable_mcd.iterable.dir/foldable/iterable_mcd/iterable.cpp.o: In function `void std::__1::vector<int, std::__1::allocator<int> >::__push_back_slow_path<int const&>(int const&)':  
/home/gdev/Downloads/hana-0.4.0/test/foldable/iterable_mcd/iterable.cpp:(.text._ZNSt3__16vectorIiNS_9allocatorIiEEE21__push_back_slow_pathIRKiEEvOT_[_ZNSt3__16vectorIiNS_9allocatorIiEEE21__push_back_slow_pathIRKiEEvOT_]+0xce): undefined reference to `std::__1::__vector_base_common<true>::__throw_length_error() const'  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
make[2]: *** [test/test.foldable.iterable_mcd.iterable] Error 1  
make[1]: *** [test/CMakeFiles/test.foldable.iterable_mcd.iterable.dir/all] Error 2  
```  
  
The full output is [here](https://gist.github.com/Syntaf/9cd3f3965bd2464175e0)  
  
Does anyone have ideas as to what I'm doing wrong?

---

## Comment 1

> Username: Syntaf  
> Created at: 2015-09-24 21:50:30 UTC  
> Url: https://github.com/boostorg/hana/issues/182#issuecomment-143059846  

It was an issue from libc++ not being used when compiling

---

## Comment 2

> Username: ldionne  
> Created at: 2015-09-24 21:53:33 UTC  
> Url: https://github.com/boostorg/hana/issues/182#issuecomment-143060374  

Did you have to modify the `CMakeLists.txt` for it to work, or just the `LDFLAGS`, or something else? If you had to modify `CMakeLists.txt`, this is a valid bug in the build system.

---

## Comment 3

> Username: Syntaf  
> Created at: 2015-09-24 21:58:06 UTC  
> Url: https://github.com/boostorg/hana/issues/182#issuecomment-143061276  

I ended up using `-DCMAKE_CXX_FLAGS="${CMAKE_CXX_FLAGS} -stdlib=libc++` on top of setting my compiler to clang++.   
  
I had seen the line `-- Performing Test BOOST_HANA_HAS_STDLIB_LIBCXX - Success` and thought it would add the flag for me, so I didn't originally think of adding it
