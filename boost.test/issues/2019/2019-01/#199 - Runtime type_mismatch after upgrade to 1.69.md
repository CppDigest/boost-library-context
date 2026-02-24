# #199 - Runtime type_mismatch after upgrade to 1.69 [Closed]

> Username: floli  
> Created at: 2019-01-25 09:23:40 UTC  
> Updated at: 2019-02-18 21:21:16 UTC  
> Closed at: 2019-02-18 21:20:51 UTC  
> Url: https://github.com/boostorg/test/issues/199  

Hello,  
  
after my distribution (Arch) rolled out the upgrade to Boost 1.69 I am unable to compile my application anymore.  
  
I try to get the log_level set. Minimal compiling example:  
  
```  
#define BOOST_TEST_DYN_LINK  
#include <boost/test/unit_test.hpp>  
#include <boost/test/unit_test_parameters.hpp>  
  
bool init_unit_test()  
{  
  using namespace boost::unit_test;  
    
  auto logLevel = runtime_config::get<log_level>(runtime_config::btrt_log_level);  
  return true;  
}  
  
int main(int argc, char* argv[])  
{  
  int retCode = boost::unit_test::unit_test_main( &init_unit_test, argc, argv );  
  return retCode;  
}  
```  
  
which compiles fine with `g++ -std=c++11 -lboost_unit_test_framework boosttesting.cpp` or with `clang++`  
  
but gives an runtime error (exact same for both compilers):  
```  
 % ./a.out   
Test setup error: boost::runtime::arg_type_mismatch: Access with invalid type for argument corresponding to parameter log_level  
```  
```  
% g++ --version  
g++ (GCC) 8.2.1 20181127  
  
% clang++ --version  
clang version 7.0.1 (tags/RELEASE_701/final)  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
```  
  
Thanks!

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-02-03 11:17:44 UTC  
> Url: https://github.com/boostorg/test/issues/199#issuecomment-460042957  

Hi,  
  
Thanks for the report. Since the error is pretty weird, this might be due to the numerous issues that popped out when the visibility default have changed on the boost project. The symbol `btrt_log_level` might be seen differently inside the shared library and outside (in your test module).  
  
I have fixed many of those, the fixes are in the `develop` branch.   
  
1. Can you reproduce the issue when you statically link to the library? (I hope this is part of the distribution)  
1. Would it be possible for you to give a try on that branch?   
  
If this does not work for you, I'll open a branch for fixing this.  
  
Thanks!

---

## Comment 2

> Username: floli  
> Created at: 2019-02-05 12:02:28 UTC  
> Url: https://github.com/boostorg/test/issues/199#issuecomment-460613294  

I would love to test and I think my distribution (Arch) also ships the static files (`*.a`). Could you give some advice on how to statically link?  
  
I removed the `#define`, of course, but I am unsure about the compiler command line:  
```  
 g++ -std=c++11  boosttesting.cpp /usr/lib/libboost_test_exec_monitor.a -lboost_unit_test_framework         
boosttesting.cpp: In Funktion »int main(int, char**)«:  
boosttesting.cpp:16:51: Fehler: ungültige Umwandlung von »bool (*)()« in »boost::unit_test::init_unit_test_func« {aka »boost::unit_test::test_suite* (*)(int, char**)«} [-fpermissive]  
   int retCode = boost::unit_test::unit_test_main( &init_unit_test, argc, argv );  
                                                   ^~~~~~~~~~~~~~~  
In file included from boosttesting.cpp:2:  
/usr/include/boost/test/unit_test.hpp:45:57: Anmerkung:   Argument 1 von »int boost::unit_test::unit_test_main(boost::unit_test::init_unit_test_func, int, char**)« wird initialisiert  
 int BOOST_TEST_DECL unit_test_main( init_unit_test_func init_func, int argc, char* argv[] );  
                                     ~~~~~~~~~~~~~~~~~~~~^~~~~~~~~  
```

---

## Comment 3

> Username: Summerdave  
> Created at: 2019-02-05 18:20:54 UTC  
> Url: https://github.com/boostorg/test/issues/199#issuecomment-460746864  

@floli  
Your code will not work with system boost.test, as main is already compiled into `libboost_unit_test_framework.a` (See [here](https://www.boost.org/doc/libs/1_69_0/libs/test/doc/html/boost_test/adv_scenarios/static_lib_customizations/entry_point.html)). You would have to rebuild boost with `-DBOOST_ALTERNATIVE_INIT_API` (and also `#define` it in your code).   
@raffienficiaud   
I investigated the issue using   
```  
#define BOOST_TEST_MODULE example  
#include <iostream>  
#include <boost/test/unit_test.hpp>  
#include <boost/test/unit_test_parameters.hpp>  
  
BOOST_AUTO_TEST_CASE(failDyn)  
{  
    using namespace boost::unit_test;  
    std::cout << runtime_config::get<log_level>( runtime_config::btrt_log_level ) << std::endl;  
}  
```  
For static linking I used `g++ $BOOST_ROOT/lib/libboost_unit_test_framework.a example.cpp`, for dynamic linking I used `g++ $BOOST_ROOT/lib/libboost_unit_test_framework.so -Wl,-rpath=$BOOST_ROOT/lib -DBOOST_TEST_DYN_LINK example.cpp`.  
Testing was done on the `develop` branch, commit `aa2ffbf1`. The statically linked variant worked just fine. The dynamically linked variant failed with:  
```  
fatal error: in "failDyn": boost::runtime::arg_type_mismatch: Access with invalid type for argument corresponding to parameter log_level  
```  
This is really odd.

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2019-02-05 18:38:59 UTC  
> Url: https://github.com/boostorg/test/issues/199#issuecomment-460753343  

Thanks for the feedback!  
  
May I ask you to try again by replacing  
  
```  
enum log_level {  
```  
  
with  
```  
enum BOOST_SYMBOL_VISIBLE log_level {  
```  
  
in the file `$BOOST_ROOT/libs/test/include/boost/test/detail/log_level.hpp` ?  
  
The assertion is fired by a mismatch of the `RTTI` for the `log_level` symbol, my guess is that it is seen differently from within and outside the shared library.

---

## Comment 5

> Username: Summerdave  
> Created at: 2019-02-05 19:11:45 UTC  
> Url: https://github.com/boostorg/test/issues/199#issuecomment-460765128  

I tried it but unfortunately the error persists.

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2019-02-06 20:01:27 UTC  
> Url: https://github.com/boostorg/test/issues/199#issuecomment-461166343  

Thanks for the feedback. Would you please check the branch `topic/GH-199-runtime-type_mismatch-after-upgrade` ?  
  
Thanks!

---

## Comment 7

> Username: Summerdave  
> Created at: 2019-02-07 15:47:37 UTC  
> Url: https://github.com/boostorg/test/issues/199#issuecomment-461478924  

Works like a charm :).  
Thank You!

---

## Comment 8

> Username: Summerdave  
> Created at: 2019-02-07 15:49:27 UTC  
> Url: https://github.com/boostorg/test/issues/199#issuecomment-461479783  

Is there a workaround for code that uses Boost.Test <= 1.69 (It was broken since 1.64)?

---

## Comment 9

> Username: raffienficiaud  
> Created at: 2019-02-07 19:18:11 UTC  
> Url: https://github.com/boostorg/test/issues/199#issuecomment-461559962  

@Summerdave was it broken since 1.64? I believe the default to hidden symbols was for 1.67/1.68?  
  
Anyway, several options, but none of them will be pleasant to hear :)  
  
1. if you are able to compile the library yourself, you may ask for default visibility by compiling with (see [here](https://www.boost.org/users/history/version_1_69_0.html)):   
    ```  
    To disable that feature you can use use a command line ./b2 visibility=global to build.  
    ```  
  
1. Applying this fix to boost.test?  
1. Otherwise linking to the static library until you get the fix might be a possibility,  
1. Using the header only variant might be another one?  
1. Having your own way of parsing the command line (will add some doc on how to do it for 1.70)?  
  
None of those solution can use the symbol that you were consuming, it is just "hidden" inside the boundaries of the shared library. I am sorry about that.

---

## Comment 10

> Username: floli  
> Created at: 2019-02-08 09:46:20 UTC  
> Url: https://github.com/boostorg/test/issues/199#issuecomment-461746838  

> @Summerdave was it broken since 1.64? I believe the default to hidden symbols was for 1.67/1.68?  
  
No, it worked fine before Boost 1.69 and will likely work on 1.70. We will find a workaround, thanks for your help @raffienficiaud

---

## Comment 11

> Username: Summerdave  
> Created at: 2019-02-08 22:37:07 UTC  
> Updated at: 2019-02-08 22:37:20 UTC  
> Url: https://github.com/boostorg/test/issues/199#issuecomment-461970947  

> @Summerdave was it broken since 1.64? I believe the default to hidden symbols was for 1.67/1.68?  
  
You are right. I compiled Boost.Test with a (very) recent b2 when i checked out the old tag.  
  
Anyway, thank you very much.

---

## Comment 12

> Username: floli  
> Created at: 2019-02-10 11:47:09 UTC  
> Url: https://github.com/boostorg/test/issues/199#issuecomment-462125554  

I think @Summerdave referred to a conditional compile we had in our project because names changed from 1.64 to 1.65. See https://github.com/precice/precice/commit/41c8a58e56486b9a002ba4712d82af1046d6a0ba#diff-056d65e350602d6404f094b31127c8d9 where the workaround was removed, because we lifted our minimal version requirement to 1.65.
