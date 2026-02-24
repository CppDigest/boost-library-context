# #27 - May be uninitialized warnings [Closed]

> Username: Kojoley  
> Created at: 2018-10-18 23:28:12 UTC  
> Updated at: 2020-03-28 01:46:38 UTC  
> Closed at: 2020-03-28 01:46:37 UTC  
> Url: https://github.com/boostorg/function/issues/27  

```  
gcc.compile.c++ bin.v2\libs\function\test\function_n_test.test\gcc-8.2.0\release\cxxstd-03-iso\visibility-hidden\function_n_test.o  
In file included from ./boost/function/detail/maybe_include.hpp:15,  
                 from ./boost/function/function0.hpp:11,  
                 from ./boost/test/execution_monitor.hpp:28,  
                 from ./boost/test/impl/execution_monitor.ipp:28,  
                 from ./boost/test/minimal.hpp:53,  
                 from libs\function\test\function_n_test.cpp:10:  
./boost/function/function_template.hpp: In function 'void test_construct_destroy_count()':  
./boost/function/function_template.hpp:998:24: error: '*((void*)& f +8)' may be used uninitialized in this function [-Werror=maybe-uninitialized]  
             std::memcpy(this->functor.data, f.functor.data, sizeof(this->functor.data));  
             ~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cc1plus.exe: all warnings being treated as errors  
  
    "g++"   -std=c++03 -fvisibility-inlines-hidden -m64 -O3 -finline-functions -Wno-inline -Wall -Werror -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DNDEBUG  -I"." -c -o "bin.v2\libs\function\test\function_n_test.test\gcc-8.2.0\release\cxxstd-03-iso\visibility-hidden\function_n_test.o" "libs\function\test\function_n_test.cpp"  
  
...failed gcc.compile.c++ bin.v2\libs\function\test\function_n_test.test\gcc-8.2.0\release\cxxstd-03-iso\visibility-hidden\function_n_test.o...  
...skipped <pbin.v2\libs\function\test\function_n_test.test\gcc-8.2.0\release\cxxstd-03-iso\visibility-hidden>function_n_test.exe for lack of <pbin.v2\libs\function\test\function_n_test.test\gcc-8.2.0\release\cxxstd-03-iso\visibility-hidden>function_n_test.o...  
...skipped <pbin.v2\libs\function\test\function_n_test.test\gcc-8.2.0\release\cxxstd-03-iso\visibility-hidden>function_n_test.run for lack of <pbin.v2\libs\function\test\function_n_test.test\gcc-8.2.0\release\cxxstd-03-iso\visibility-hidden>function_n_test.exe...  
gcc.compile.c++ bin.v2\libs\function\test\function_n_test.test\gcc-8.2.0\release\cxxstd-11-iso\visibility-hidden\function_n_test.o  
In file included from ./boost/function/detail/maybe_include.hpp:15,  
                 from ./boost/function/function0.hpp:11,  
                 from ./boost/test/execution_monitor.hpp:28,  
                 from ./boost/test/impl/execution_monitor.ipp:28,  
                 from ./boost/test/minimal.hpp:53,  
                 from libs\function\test\function_n_test.cpp:10:  
./boost/function/function_template.hpp: In function 'void test_construct_destroy_count()':  
./boost/function/function_template.hpp:998:24: error: '*((void*)& f +8)' may be used uninitialized in this function [-Werror=maybe-uninitialized]  
             std::memcpy(this->functor.data, f.functor.data, sizeof(this->functor.data));  
             ~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cc1plus.exe: all warnings being treated as errors  
  
    "g++"   -std=c++11 -fvisibility-inlines-hidden -m64 -O3 -finline-functions -Wno-inline -Wall -Werror -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DNDEBUG  -I"." -c -o "bin.v2\libs\function\test\function_n_test.test\gcc-8.2.0\release\cxxstd-11-iso\visibility-hidden\function_n_test.o" "libs\function\test\function_n_test.cpp"  
  
...failed gcc.compile.c++ bin.v2\libs\function\test\function_n_test.test\gcc-8.2.0\release\cxxstd-11-iso\visibility-hidden\function_n_test.o...  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2018-10-20 18:28:26 UTC  
> Url: https://github.com/boostorg/function/issues/27#issuecomment-431606839  

Technically correct, but I'm not sure what we can do about these. Value-initializing `functor` in the constructor will be too expensive.

---

## Comment 2

> Username: jeking3  
> Created at: 2018-10-28 01:04:40 UTC  
> Url: https://github.com/boostorg/function/issues/27#issuecomment-433667800  

`-fvisibility-inlines-hidden` is not a common setting to use, right?

---

## Comment 3

> Username: jeking3  
> Created at: 2018-10-28 01:05:14 UTC  
> Url: https://github.com/boostorg/function/issues/27#issuecomment-433667830  

Does it happen with `cxxstd=11` or later?

---

## Comment 4

> Username: Kojoley  
> Created at: 2018-10-28 01:09:00 UTC  
> Url: https://github.com/boostorg/function/issues/27#issuecomment-433667985  

> `-fvisibility-inlines-hidden` is not a common setting to use, right?  
  
It is default flags for `./b2 -j3 toolset=gcc-8.2.0 cxxstd=03,11 libs/function/test warnings=on warnings-as-errors=on variant=debug,release`  
  
> Does it happen with `cxxstd=11` or later?  
  
Yes, it is shown in the first post.

---

## Comment 5

> Username: jeking3  
> Created at: 2018-10-28 01:20:20 UTC  
> Url: https://github.com/boostorg/function/issues/27#issuecomment-433668427  

Thanks - I missed the second half.   I blame my cold.

---

## Comment 6

> Username: pdimov  
> Created at: 2020-03-28 01:46:37 UTC  
> Url: https://github.com/boostorg/function/issues/27#issuecomment-605376950  

Should be fixed by #38.
