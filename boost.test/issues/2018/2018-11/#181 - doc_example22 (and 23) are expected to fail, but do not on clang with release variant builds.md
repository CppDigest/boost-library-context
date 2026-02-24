# #181 - doc_example22 (and 23) are expected to fail, but do not on clang with release variant builds [Closed]

> Username: jeking3  
> Created at: 2018-11-11 12:18:27 UTC  
> Updated at: 2019-02-08 17:25:42 UTC  
> Closed at: 2019-02-08 13:43:34 UTC  
> Url: https://github.com/boostorg/test/issues/181  

Clang 6, cxxstd=2a: https://travis-ci.org/jeking3/test/jobs/453349509#L3563  
Clang 5, cxxstd=17: https://travis-ci.org/jeking3/test/jobs/453349504#L3450  
Clang 4, cxxstd=14: https://travis-ci.org/jeking3/test/jobs/453349500#L3418  
  
```  
boost@82aef92a4871:/boost/libs/test/test$ clang++-6.0 --version  
clang version 6.0.0-1ubuntu2 (tags/RELEASE_600/final)  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
  
boost@82aef92a4871:/boost/libs/test/test$ ../../../b2 variant=release cxxstd=17 toolset=clang doc-example22  
Performing configuration checks  
  
    - BOOST_COMP_GNUC >= 4.3.0 : no  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
    - Boost.Config Feature Check: cxx11_decltype : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_random : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_tuple : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_initializer_list : yes (cached)  
    - Boost.Config Feature Check: cxx11_variadic_macros : yes (cached)  
    - Boost.Config Feature Check: cxx11_template_aliases : yes (cached)  
    - BOOST_COMP_GNUC >= 4.3.0 : no  (cached)  
    - Boost.Config Feature Check: cxx11_decltype : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_random : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_tuple : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_initializer_list : yes (cached)  
    - Boost.Config Feature Check: cxx11_variadic_macros : yes (cached)  
    - Boost.Config Feature Check: cxx11_template_aliases : yes (cached)  
...found 1990 targets...  
...updating 13 targets...  
clang-linux.compile.c++.without-pth ../../../bin.v2/libs/test/test/doc-example22.test/clang-linux-6.0/release/cxxstd-17-iso/visibility-hidden/example22.run-fail.o  
clang-linux.compile.c++.without-pth ../../../bin.v2/libs/system/build/clang-linux-6.0/release/cxxstd-17-iso/visibility-hidden/error_code.o  
clang-linux.link.dll ../../../bin.v2/libs/system/build/clang-linux-6.0/release/cxxstd-17-iso/visibility-hidden/libboost_system.so.1.69.0  
clang-linux.link ../../../bin.v2/libs/test/test/doc-example22.test/clang-linux-6.0/release/cxxstd-17-iso/visibility-hidden/doc-example22  
testing.capture-output ../../../bin.v2/libs/test/test/doc-example22.test/clang-linux-6.0/release/cxxstd-17-iso/visibility-hidden/doc-example22.run  
...failed testing.capture-output ../../../bin.v2/libs/test/test/doc-example22.test/clang-linux-6.0/release/cxxstd-17-iso/visibility-hidden/doc-example22.run...  
...removing ../../../bin.v2/libs/test/test/doc-example22.test/clang-linux-6.0/release/cxxstd-17-iso/visibility-hidden/doc-example22.run  
...failed updating 1 target...  
...skipped 2 targets...  
...updated 10 targets...  
  
boost@82aef92a4871:/boost/libs/test/test$ ../../../bin.v2/libs/test/test/doc-example22.test/clang-linux-6.0/release/cxxstd-17-iso/visibility-hidden/doc-example  
22  
Running 1 test case...  
  
*** No errors detected  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2018-11-11 22:40:21 UTC  
> Url: https://github.com/boostorg/test/issues/181#issuecomment-437712041  

We identified the boost test matrix is not running release builds for clang, so I reported it to the mailing list.  That said, these tests will need to be addressed.

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2019-02-04 06:35:40 UTC  
> Url: https://github.com/boostorg/test/issues/181#issuecomment-460144646  

I believe this is also fixed in develop now.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2019-02-08 13:43:34 UTC  
> Url: https://github.com/boostorg/test/issues/181#issuecomment-461806788  

In master, closing. Thanks!
