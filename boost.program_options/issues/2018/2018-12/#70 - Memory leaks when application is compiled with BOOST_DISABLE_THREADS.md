# #70 - Memory leaks when application is compiled with BOOST_DISABLE_THREADS [Open]

> Username: nomis  
> Created at: 2018-12-21 13:00:24 UTC  
> Updated at: 2018-12-23 16:07:28 UTC  
> Url: https://github.com/boostorg/program_options/issues/70  

There's a memory leak if an application is compiled with BOOST_DISABLE_THREADS, is this expected behaviour?  
  
test.cc:  
``` c++  
#include <boost/program_options.hpp>  
  
int main(int argc, char *argv[]) {  
        boost::program_options::options_description opts{"Test"};  
        opts.add_options()  
                ("help,h", boost::program_options::bool_switch(), "display this help and exit");  
  
        return 0;  
}  
```  
  
Without `BOOST_DISABLE_THREADS` defined:  
```  
$ g++ -fsanitize=address  test.cc  -lboost_program_options -lboost_system -o test  
$ ./test  
$  
```  
  
With `BOOST_DISABLE_THREADS` defined:  
```  
$ g++ -fsanitize=address -DBOOST_DISABLE_THREADS  test.cc  -lboost_program_options -lboost_system -o test  
=================================================================  
==97000==ERROR: LeakSanitizer: detected memory leaks  
  
Direct leak of 24 byte(s) in 1 object(s) allocated from:  
    #0 0x7f6757a39458 in operator new(unsigned long) (/usr/lib/x86_64-linux-gnu/libasan.so.4+0xe0458)  
    #1 0x7f675771777f in boost::program_options::options_description_easy_init::operator()(char const*, boost::program_options::value_semantic const*, char const*) (/usr/lib/x86_64-linux-gnu/libboost_program_options.so.1.65.1+0x3f77f)  
  
Indirect leak of 176 byte(s) in 1 object(s) allocated from:  
    #0 0x7f6757a39458 in operator new(unsigned long) (/usr/lib/x86_64-linux-gnu/libasan.so.4+0xe0458)  
    #1 0x7f6757732624 in boost::program_options::bool_switch(bool*) (/usr/lib/x86_64-linux-gnu/libboost_program_options.so.1.65.1+0x5a624)  
  
Indirect leak of 120 byte(s) in 1 object(s) allocated from:  
    #0 0x7f6757a39458 in operator new(unsigned long) (/usr/lib/x86_64-linux-gnu/libasan.so.4+0xe0458)  
    #1 0x7f6757717761 in boost::program_options::options_description_easy_init::operator()(char const*, boost::program_options::value_semantic const*, char const*) (/usr/lib/x86_64-linux-gnu/libboost_program_options.so.1.65.1+0x3f761)  
    #2 0x7f6756d67b96 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21b96)  
  
Indirect leak of 27 byte(s) in 1 object(s) allocated from:  
    #0 0x7f6757a39458 in operator new(unsigned long) (/usr/lib/x86_64-linux-gnu/libasan.so.4+0xe0458)  
    #1 0x7f67577155fc  (/usr/lib/x86_64-linux-gnu/libboost_program_options.so.1.65.1+0x3d5fc)  
  
Indirect leak of 24 byte(s) in 1 object(s) allocated from:  
    #0 0x7f6757a39458 in operator new(unsigned long) (/usr/lib/x86_64-linux-gnu/libasan.so.4+0xe0458)  
    #1 0x7f6757716593 in boost::program_options::option_description::option_description(char const*, boost::program_options::value_semantic const*, char const*) (/usr/lib/x86_64-linux-gnu/libboost_program_options.so.1.65.1+0x3e593)  
  
Indirect leak of 16 byte(s) in 1 object(s) allocated from:  
    #0 0x7f6757a39458 in operator new(unsigned long) (/usr/lib/x86_64-linux-gnu/libasan.so.4+0xe0458)  
    #1 0x7f67577326c5 in boost::program_options::bool_switch(bool*) (/usr/lib/x86_64-linux-gnu/libboost_program_options.so.1.65.1+0x5a6c5)  
  
SUMMARY: AddressSanitizer: 387 byte(s) leaked in 6 allocation(s).  
```

---

## Comment 1

> Username: nomis  
> Created at: 2018-12-22 13:31:41 UTC  
> Url: https://github.com/boostorg/program_options/issues/70#issuecomment-449570456  

There's a `boost::shared_ptr` being constructed within the `program_options` library that is released using code from the header files for `shared_ptr`:  
```  
shared_count ./boost/smart_ptr/detail/shared_count.hpp:126 0x55555576a3c8  
sp_pointer_construct ./boost/smart_ptr/shared_ptr.hpp:287 ctor 0x55555576a3c0 for 0x55555576a280  
shared_count ./boost/smart_ptr/detail/shared_count.hpp:142 0x7fffffffda00  
sp_counted_base ./boost/smart_ptr/detail/sp_counted_base_std_atomic.hpp:75 0x55555576a410  
sp_counted_impl_p ./boost/smart_ptr/detail/sp_counted_impl.hpp:84 ctor 0x55555576a410 for 0x55555576a280  
~shared_count ./boost/smart_ptr/detail/shared_count.hpp:436 0x7fffffffda00 (pi_=(nil))  
shared_count ./boost/smart_ptr/detail/shared_count.hpp:126 0x7fffffffda68  
sp_pointer_construct ./boost/smart_ptr/shared_ptr.hpp:287 ctor 0x7fffffffda60 for 0x55555576a360  
shared_count ./boost/smart_ptr/detail/shared_count.hpp:142 0x7fffffffda70  
sp_counted_base ./boost/smart_ptr/detail/sp_counted_base_std_atomic.hpp:75 0x55555576a4b0  
sp_counted_impl_p ./boost/smart_ptr/detail/sp_counted_impl.hpp:84 ctor 0x55555576a4b0 for 0x55555576a360 <-- created here  
~shared_count ./boost/smart_ptr/detail/shared_count.hpp:436 0x7fffffffda70 (pi_=(nil))  
shared_count ./boost/smart_ptr/detail/shared_count.hpp:448 0x7fffffffda78  
shared_count ./boost/smart_ptr/detail/shared_count.hpp:448 0x55555576a4d8  
~shared_ptr ./boost/smart_ptr/shared_ptr.hpp:356 dtor 0x7fffffffda70  
~shared_count ./boost/smart_ptr/detail/shared_count.hpp:436 0x7fffffffda78 (pi_=0x55555576a4b0)  
release ./boost/smart_ptr/detail/sp_counted_base_std_atomic.hpp:112 0x55555576a4b0 use_count_=3  
~shared_ptr ./boost/smart_ptr/shared_ptr.hpp:356 dtor 0x7fffffffda60  
~shared_count ./boost/smart_ptr/detail/shared_count.hpp:436 0x7fffffffda68 (pi_=0x55555576a4b0)  
release ./boost/smart_ptr/detail/sp_counted_base_std_atomic.hpp:112 0x55555576a4b0 use_count_=2  
```  
  
When the objects go out of scope the `shared_ptr` is released:  
```  
~shared_ptr /home/simon/inst/boost-1.69.0/include/boost/smart_ptr/shared_ptr.hpp:356 dtor 0x562d48dca4d0  
~shared_count /home/simon/inst/boost-1.69.0/include/boost/smart_ptr/detail/shared_count.hpp:436 0x562d48dca4d8 (pi_=0x562d48dca4b0)  
release /home/simon/inst/boost-1.69.0/include/boost/smart_ptr/detail/sp_counted_base_std_atomic.hpp:112 0x562d48dca4b0 use_count_=1 <-- released here  
dispose ./boost/smart_ptr/detail/sp_counted_impl.hpp:92 0x562d48dca4b0 dispose 0x562d48dca360  
~shared_ptr ./boost/smart_ptr/shared_ptr.hpp:356 dtor 0x562d48dca3c0  
~shared_count ./boost/smart_ptr/detail/shared_count.hpp:436 0x562d48dca3c8 (pi_=0x562d48dca410)  
release ./boost/smart_ptr/detail/sp_counted_base_std_atomic.hpp:112 0x562d48dca410 use_count_=1  
dispose ./boost/smart_ptr/detail/sp_counted_impl.hpp:92 0x562d48dca410 dispose 0x562d48dca280  
destroy ./boost/smart_ptr/detail/sp_counted_base_std_atomic.hpp:92 0x562d48dca410  
~sp_counted_base ./boost/smart_ptr/detail/sp_counted_base_std_atomic.hpp:80 0x562d48dca410  
destroy ./boost/smart_ptr/detail/sp_counted_base_std_atomic.hpp:92 0x562d48dca4b0  
~sp_counted_base ./boost/smart_ptr/detail/sp_counted_base_std_atomic.hpp:80 0x562d48dca4b0  
```  
  
If compiled with no extra symbols, then the header implementation is using `sp_counted_base_std_atomic`:  
```  
boost::detail::sp_counted_base::release (this=0x55555576a4b0) at /home/simon/inst/boost-1.69.0/include/boost/smart_ptr/detail/sp_counted_base_std_atomic.hpp:112  
112		printf("%s %s:%d %p use_count_=%ld\n", __func__, __FILE__, __LINE__, this, use_count());  
```  
  
If compiled with `BOOST_DISABLE_THREADS`, then the header implementation is using `sp_counted_base_nt` which does not handle the `use_count_` field in the same way, so the use count doesn't go to 0 and the instance is not deleted:  
```  
boost::detail::sp_counted_base::release (this=0x55555576a4b0) at /home/simon/inst/boost-1.69.0/include/boost/smart_ptr/detail/sp_counted_base_nt.hpp:80  
80	        if( --use_count_ == 0 )  
(gdb) p use_count_  
$3 = 4294967297  
```

---

## Comment 2

> Username: nomis  
> Created at: 2018-12-22 13:36:00 UTC  
> Updated at: 2018-12-22 13:59:21 UTC  
> Url: https://github.com/boostorg/program_options/issues/70#issuecomment-449570740  

There are a lot of other implementations of `boost::shared_ptr` other than these two, some of which are selected based on the compiler being used. If they don't match exactly between compilation of the library and the application then `program_options` (and probably other areas of Boost) will leak memory.  
  
The `shared_ptr` with this issue is `m_value_semantic` within the `boost::program_options::option_description` constructor, which is created using the library implementation but destroyed using the application implementation.

---

## Comment 3

> Username: vprus  
> Created at: 2018-12-22 17:45:22 UTC  
> Url: https://github.com/boostorg/program_options/issues/70#issuecomment-449586976  

I would say that in general, threading options should be the same when building the library and using the library; they affect the ABI

---

## Comment 4

> Username: nomis  
> Created at: 2018-12-22 17:46:24 UTC  
> Url: https://github.com/boostorg/program_options/issues/70#issuecomment-449587026  

Then the type names should be different so that it's not possible to link with the wrong implementation.

---

## Comment 5

> Username: vprus  
> Created at: 2018-12-22 17:54:10 UTC  
> Url: https://github.com/boostorg/program_options/issues/70#issuecomment-449587447  

Sorry, type names of what?  
  
This is generally the way C++ works - if a library and an application using that library are built with different options, things can break.   
  
This is also the case with other Boost libraries - if use mix different build settings, you will run into trouble. Using autolink on Windows handles some cases, but is not guaranteed to handle arbitrary mismatches, and on Linux, you need to use the right library.

---

## Comment 6

> Username: nomis  
> Created at: 2018-12-22 17:57:06 UTC  
> Url: https://github.com/boostorg/program_options/issues/70#issuecomment-449587648  

If the type names of `boost::shared_ptr` were modified by the headers so that the implementation had to match then it would not be possible to link to a boost library that had been compiled with a different (potentially incompatible) implementation.  
  
This is not the way C++ works. If you specify the types in the same way in both the library and the application then it will work correctly. The library can be implemented differently. Boost should not be overloading the same type name with different incompatible implementations.

---

## Comment 7

> Username: vprus  
> Created at: 2018-12-22 18:22:01 UTC  
> Url: https://github.com/boostorg/program_options/issues/70#issuecomment-449589148  

It seems your suggestion and concerns are mostly about shared_ptr itself, and @pdimov should be able to respond to those.

---

## Comment 8

> Username: vprus  
> Created at: 2018-12-22 19:27:48 UTC  
> Url: https://github.com/boostorg/program_options/issues/70#issuecomment-449592674  

Regarding program_options, it makes no guarantee about ABI compatibility between different build variants and compilation options. In general, if you arbitrarily change compiler options, ABI can break - either because such options directly change ABI that compiler uses, or because it changes library code, including in dependency libraries. In case of `BOOST_DISABLE_TRHEADS`, it's quite reasonable that implementation changes. While it's possible to mitigate some of such cases, it's not automatic and it's not easy. While it's possible to detect some such cases, it's not automatic or easy either. Therefore, I'd say that problems when mixing single- and multi- threaded versions of program_options do not constitute a bug - such problems are expected.

---

## Comment 9

> Username: pdimov  
> Created at: 2018-12-22 19:38:51 UTC  
> Url: https://github.com/boostorg/program_options/issues/70#issuecomment-449593299  

Even if boost::shared_ptr were named differently, this problem would still have persisted, because option_description is named the same. Preventing this kind of ODR violation would require the name changes to bubble up to the library using shared_ptr, to the library using that library, and so on and so forth. And if shared_ptr changes its naming scheme, all those libraries need to be modified in concert.  
  
TL;DR this is not practical.  
  
Now all that said, shared_ptr is actually intended to allow this use; but since I don't have a test for it and it's a very niche use case, the fact that `long` being 64 bits nowadays breaking it has escaped me. I'll attempt a fix for the next release. (Linking between -std=c++03 and -std=c++11 needs also be looked at.)

---

## Comment 10

> Username: pdimov  
> Created at: 2018-12-23 16:07:28 UTC  
> Url: https://github.com/boostorg/program_options/issues/70#issuecomment-449646112  

Should be fixed by https://github.com/boostorg/smart_ptr/commit/2932ca420340d9be59380bf7d30e92ae9dcea606.
