# #2149 - Can't build examples [Closed]

> Username: khajiit-osx  
> Created at: 2021-01-13 20:35:18 UTC  
> Updated at: 2022-01-09 05:17:09 UTC  
> Closed at: 2022-01-09 05:17:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2149  

First off, thanks so much for a fantastic repo.  
  
### Version of Beast  
newest (0.8.2)  
  
### Steps necessary to reproduce the problem  
  
I'm on Ubuntu. When recursively cloning the Boost superproj, running ./bootstrap and then ./b2 headers and then ./b2, I proceed to follow Vinnie's instructions on his readme for Beast, which he says will build the examples (at least the third line will):  
  
```  
export PATH=$PWD:$PATH  
b2 -j2 libs/beast/test cxxstd=11      # bjam must be in your $PATH  
b2 -j2 libs/beast/example cxxstd=11   # "-j2" means use two processors  
b2 libs/beast/doc                     # Doxygen and Saxon are required for this  
```  
  
When doing this, I get the following output   
  
```  
Performing configuration checks  
  
    - default address-model    : none (cached)  
    - default architecture     : none (cached)  
    - symlinks supported       : yes (cached)  
    - cxx11_constexpr          : no  (cached)  
    - cxx11_decltype           : no  (cached)  
    - cxx11_hdr_tuple          : no  (cached)  
    - cxx11_template_aliases   : no  (cached)  
    - cxx11_variadic_templates : no  (cached)  
...found 1 target...  
```  
  
But no binaries for the examples. I'm not sure what I'm missing?  
  
### All relevant compiler information  
g++ on Ubuntu 18.04

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-01-13 20:39:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2149#issuecomment-759725479  

Hmm there’s something odd going on here. The config output suggests that the compiler is not c++11 compatible.

---

## Comment 2

> Username: khajiit-osx  
> Created at: 2021-01-13 20:43:30 UTC  
> Updated at: 2021-01-13 20:43:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2149#issuecomment-759727743  

Wow that was really fast, thank you. I'm working on an AWS workspace and have been compiling with c++11 but all compiling has been abstracted away so I'm not sure exactly how my firm is doing it (I just joined as an intern last week). Is there a way to change the compiler settings used to build with b2?

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-01-13 20:48:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2149#issuecomment-759730112  

Some documentation here  
https://boostorg.github.io/build/manual/develop/index.html#bbv2.overview.configuration  
  
I’m not a world expert on b2.  
  
a good place to ask for help is in the #boost channel of cpplang slack

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-01-13 20:49:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2149#issuecomment-759730871  

I guess start by getting b2 to dump debug information. That should provide clues about which compiler it’s found (if any)

---

## Comment 5

> Username: khajiit-osx  
> Created at: 2021-01-13 23:16:25 UTC  
> Updated at: 2021-01-13 23:23:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2149#issuecomment-759818552  

Thanks for the advice, after a few hours I finally got at least the b2 step to work--although I honestly have no clue what the fix was because all I did was reclone the boost superproj and follow the same steps as previously mentioned (`./boostrap.sh`, `b2 headers`, and then `b2`). The end of the output from the last command was  
  
```  
...updated 1819 targets...  
  
  
The Boost C++ Libraries were successfully built!  
  
The following directory should be added to compiler include paths:  
  
    /mnt/shares/remotentrn/alt_root/home2/[me]/boost  
  
The following directory should be added to linker library paths:  
  
    /mnt/shares/remotentrn/alt_root/home2/[me]/boost/stage/lib  
```  
Silly question, but I still don't see any binaries in the build dir (I'm specifically looking at async-ssl, but same goes for all other examples). When I try to use either the makefile or the cmake files provided, it's unable to locate anything. I am trying my best to find online resources to address this but I'm not getting very far.  
  
```  
[  0%] Built target lib-asio-ssl  
[ 33%] Built target lib-asio  
[ 66%] Building CXX object CMakeFiles/lib-beast.dir/test/lib_beast.cpp.o  
In file included from /home2/[me]/boost/libs/beast/include/boost/beast/core/detail/bind_handler.hpp:14:0,  
                 from /home2/[me]/boost/libs/beast/include/boost/beast/core/bind_handler.hpp:14,  
                 from /home2/[me]/boost/libs/beast/include/boost/beast/_experimental/test/stream.hpp:14,  
                 from /home2/[me]/boost/libs/beast/include/boost/beast/_experimental/test/impl/stream.ipp:13,  
                 from /home2/[me]/boost/libs/beast/include/boost/beast/src.hpp:30,  
                 from /home2/[me]/boost/libs/beast/test/lib_beast.cpp:12:  
/home2/[me]/boost/libs/beast/include/boost/beast/core/detail/tuple.hpp:13:10: fatal error: boost/mp11/integer_sequence.hpp: No such file or directory  
 #include <boost/mp11/integer_sequence.hpp>  
          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
compilation terminated.  
CMakeFiles/lib-beast.dir/build.make:62: recipe for target 'CMakeFiles/lib-beast.dir/test/lib_beast.cpp.o' failed  
make[2]: *** [CMakeFiles/lib-beast.dir/test/lib_beast.cpp.o] Error 1  
CMakeFiles/Makefile2:67: recipe for target 'CMakeFiles/lib-beast.dir/all' failed  
make[1]: *** [CMakeFiles/lib-beast.dir/all] Error 2  
Makefile:83: recipe for target 'all' failed  
make: *** [all] Error 2  
```

---

## Comment 6

> Username: stale[bot]  
> Created at: 2021-05-29 16:11:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2149#issuecomment-850857908  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2149#issuecomment-1008232356  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
