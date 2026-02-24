# #230 - odr_violation [Closed]

> Username: emaxerrno  
> Created at: 2019-10-02 17:11:55 UTC  
> Updated at: 2019-11-19 18:58:42 UTC  
> Closed at: 2019-11-19 17:00:10 UTC  
> Url: https://github.com/boostorg/test/issues/230  

Using boost 1.70.0  
  
including `#include <boost/test/included/unit_test.hpp>` as stated in most docs causes an odr violation (detected in clang 8)  
  
Which is easily fixed if one changes the include for `#include <boost/test/unit_test.hpp>`  
  
```sh  
=================================================================  
==9631==ERROR: AddressSanitizer: odr-violation (0x000004af7260):  
  [1] size=24 'boost::unit_test::runtime_config::btrt_color_output' v_deps_install/include/boost/test/impl/unit_test_parameters.ipp:77:13  
  [2] size=24 'boost::unit_test::runtime_config::btrt_color_output' ./boost/test/impl/unit_test_parameters.ipp:77:13  
```

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-10-02 19:19:16 UTC  
> Url: https://github.com/boostorg/test/issues/230#issuecomment-537640381  

Hi @senior7515 and thanks for the report. I checked the symbols and I do not see how it can be defined twice. To be able to dig further, would you please provide me with the following info:  
  
* how did you build and install boost?  
* how are you building your project? in particular the header paths and the static/shared libraries that get into the final test module being generated.  
  
I am asking this because from the odr-violation report, I can see the exact same definition coming from two different files `v_deps_install/include/boost/test/impl/unit_test_parameters.ipp:77:13` and `./boost/test/impl/unit_test_parameters.ipp:77:13`.  
  
Thanks

---

## Comment 2

> Username: emaxerrno  
> Created at: 2019-10-02 20:17:59 UTC  
> Url: https://github.com/boostorg/test/issues/230#issuecomment-537662187  

hi @raffienficiaud ,   
  
building boost using cmake's external project add: (really just a bash script wrapper)   
  
```  
ExternalProject_Add(boost  
  # The 1.67.0 release has a bug in Boost Lockfree around a missing header.  
  URL https://dl.bintray.com/boostorg/release/1.70.0/source/boost_1_70_0.tar.gz  
  URL_MD5 fea771fe8176828fabf9c09242ee8c26  
  INSTALL_DIR    @V_DEPS_INSTALL_DIR@  
  PATCH_COMMAND  
    ./bootstrap.sh  
    --prefix=@V_DEPS_INSTALL_DIR@  
    --with-libraries=atomic,chrono,date_time,filesystem,program_options,system,test,thread  
    &&  
    echo  
    "${boost_user_config_jam}"  
    > <SOURCE_DIR>/tools/build/src/user-config.jam  
  CONFIGURE_COMMAND ""  
  BUILD_COMMAND ""  
  DEPENDS ${default_depends}  
  INSTALL_COMMAND  
    ${CMAKE_COMMAND} -E chdir <SOURCE_DIR>  
    ./b2  
    -j ${build_concurrency_factor}  
    --toolset=${toolset}  
    --layout=system  
    --build-dir=<BINARY_DIR>  
    install  
    variant=release  
    cflags=-fPIC  
    cxxflags=-fPIC  
    link=shared  
    ${boost_cxx_flags}  
    ${boost_link_flags}  
    threading=multi  
    hardcode-dll-paths=true  
    dll-path=@V_DEPS_INSTALL_DIR@/lib)  
  
```  
  
I will try to reproduce a small main with a valid build system sometime later this weekend , what was strange to me is that changing the header fixed the problem.   
  
Note that this happens on a hermetic build (docker image, fedora:30) so I should be able to repro it on a smaller proj soon.

---

## Comment 3

> Username: reltuk  
> Created at: 2019-11-18 23:35:23 UTC  
> Url: https://github.com/boostorg/test/issues/230#issuecomment-555260814  

AFAIK, this happens when someone uses `#include <boost/test/included/unit_test.hpp>` but then still links against `libboost_unit_test_framework`. It seems likely here, since not using the `included` header would fail if the tests were not linking against `libboost_unit_test_framework`.

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2019-11-19 06:32:09 UTC  
> Url: https://github.com/boostorg/test/issues/230#issuecomment-555355450  

I am not sure to fully understand. `<boost/test/included/unit_test.hpp>` is the header only variant of boost.test: when included that way, there is no need to link against `libboost_unit_test_framework` at all.  
  
Linking to `libboost_unit_test_framework` is required when you are using the static or shared library "variant" of boost.test, in that case, the header to include is `<boost/test/unit_test.hpp>` and there is a `define` that makes the difference between the two.  
  
All those variants are explained here: https://www.boost.org/doc/libs/1_71_0/libs/test/doc/html/boost_test/usage_variants.html  
  
In all cases, the compilation and linking should be consistent to each other: in those 3 cases (header, static, shared variants) the headers are compiled with different defines and that would cause an ODR violation if eg. you use the header only variant and then link to the shared library (my guess is that the symbols are weak, so they get replaced at runtime).  
  
@reltuk @senior7515 would you please confirm that making the build consistent solves the issue?

---

## Comment 5

> Username: emaxerrno  
> Created at: 2019-11-19 07:01:53 UTC  
> Url: https://github.com/boostorg/test/issues/230#issuecomment-555363926  

@raffienficiaud confirmed. feel free to close.

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2019-11-19 11:27:03 UTC  
> Url: https://github.com/boostorg/test/issues/230#issuecomment-555465166  

Thanks @senior7515 for the quick reply. @reltuk is it ok to close on your side?

---

## Comment 7

> Username: reltuk  
> Created at: 2019-11-19 14:37:11 UTC  
> Url: https://github.com/boostorg/test/issues/230#issuecomment-555536089  

Yes, this is good to close on my side. My comment was trying to help @senior7515 understand what might be causing his odr violation based on his setup.

---

## Comment 8

> Username: raffienficiaud  
> Created at: 2019-11-19 18:58:42 UTC  
> Url: https://github.com/boostorg/test/issues/230#issuecomment-555656401  

Thanks for the quick update!
