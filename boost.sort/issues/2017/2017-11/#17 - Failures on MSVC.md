# #17 - Failures on MSVC [Closed]

> Username: pdimov  
> Created at: 2017-11-01 12:44:42 UTC  
> Updated at: 2017-11-14 03:07:38 UTC  
> Closed at: 2017-11-14 03:07:38 UTC  
> Url: https://github.com/boostorg/sort/issues/17  

```  
testing.capture-output bin.v2\libs\sort\test\test_flat_stable_sort.test\msvc-14.1\debug\optimization-speed\threadapi-win32\threading-multi\test_flat_stable_sort.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
unknown location(0): fatal error: in "test_main_caller(_argc,_argv_)": C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.11.25503\include\vector(140) : Assertion failed: vector iterator + offset out of range  
*** 1 failure is detected in the test module "Test Program"  
   
EXIT STATUS: 201   
====== END OUTPUT ======  
```  
  
See https://ci.appveyor.com/project/boostorg/boost/build/1.0.4436-develop/job/3iuoitpwo712t9l9#L268

---

## Comment 1

> Username: spreadsort  
> Created at: 2017-11-01 22:57:53 UTC  
> Url: https://github.com/boostorg/sort/issues/17#issuecomment-341267954  

Thanks for the report!  I've asked Francisco to take a look.

---

## Comment 2

> Username: fjtapia  
> Created at: 2017-11-02 09:58:37 UTC  
> Url: https://github.com/boostorg/sort/issues/17#issuecomment-341371800  

I begin to work about it  
  
2017-11-01 23:57 GMT+01:00 spreadsort <notifications@github.com>:  
  
> Thanks for the report! I've asked Francisco to take a look.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/17#issuecomment-341267954>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ADrumAdGMTmCJT6QrhS-fBKoWZ2xuRqgks5syPdygaJpZM4QOJqK>  
> .  
>

---

## Comment 3

> Username: fjtapia  
> Created at: 2017-11-11 08:21:03 UTC  
> Url: https://github.com/boostorg/sort/issues/17#issuecomment-343648936  

Hi,  
  
The code is corrected and uploaded to boostorg/sort . The problem was the  
compiler, The VC++ Version 14 ( Visual Studio 2015), mark as error legal  
code, generate run time errors with correct code, and have a very poor  
optimization (The same code, with the same data, on the same machine spend  
60% more time than the version 15). Happily, the version 15 ( Visual Studio  
2017) correct all these problems, and have a good optimization. My initial  
code was compiled with the version 15, and didn’t have problems.  
  
One solution could be compile with the option -D_SCL_SECURE_NO_WARNINGS,  
but this impose a burden to the users for to use the algorithms. I decide  
modify the code for to be compiled with any compiler.  
  
The error marked was with the standard library functions move and move  
backward. The compiler say they are deprecated, and the error was because  
don’t use checked iterators. ( An unchecked iterator is a simple pointer).  
At end I codified a reply of the standards functions in the code. It have  
problems, too, when add a number to an iterator, even if you don’t access  
to the data pointed. I correct it in several parts of the code, and prepare  
a cmake for to to compile and run all the test functions in Windows and  
Linux  
  
I change too, my test programs for to be more short, clear, simple,.  
  
Watching the code I see unused code in the repository. The programs are in  
the folder  
  
boost/sort/spinsort. The folder util and the files spin_sort_bad.hpp,  
spin_sort_old.hpp and spin_sort_def.hpp, must be deleted. In the folder  
only must be the spinsort.hpp file. Could you do in a easy way? If not I  
must do the Monday , because I travel this weekend.  
  
2017-11-02 10:58 GMT+01:00 Francisco José Tapia <fjtapia@gmail.com>:  
  
> I begin to work about it  
>  
> 2017-11-01 23:57 GMT+01:00 spreadsort <notifications@github.com>:  
>  
>> Thanks for the report! I've asked Francisco to take a look.  
>>  
>> —  
>> You are receiving this because you are subscribed to this thread.  
>> Reply to this email directly, view it on GitHub  
>> <https://github.com/boostorg/sort/issues/17#issuecomment-341267954>, or mute  
>> the thread  
>> <https://github.com/notifications/unsubscribe-auth/ADrumAdGMTmCJT6QrhS-fBKoWZ2xuRqgks5syPdygaJpZM4QOJqK>  
>> .  
>>  
>  
>

---

## Comment 4

> Username: pdimov  
> Created at: 2017-11-11 12:00:40 UTC  
> Url: https://github.com/boostorg/sort/issues/17#issuecomment-343659860  

msvc-14.1 is now green:  
  
https://ci.appveyor.com/project/boostorg/boost/build/1.0.4718-develop  
  
but there are failures on msvc-12.0 related to `noexcept` which the compiler does not support.  
  
```  
compile-c-c++ bin.v2\libs\sort\test\test_flat_stable_sort.test\msvc-12.0\debug\optimization-speed\threadapi-win32\threading-multi\test_flat_stable_sort.obj  
cl : Command line warning D9002 : ignoring unknown option '-std=c++11'  
test_flat_stable_sort.cpp  
C:\projects\boost\boost/sort/common/util/algorithm.hpp(87) : error C2143: syntax error : missing ';' before 'const'  
C:\projects\boost\boost/sort/common/util/algorithm.hpp(87) : error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
C:\projects\boost\boost/sort/common/util/algorithm.hpp(114) : error C3646: 'noexcept' : unknown override specifier  
C:\projects\boost\boost/sort/common/util/algorithm.hpp(128) : error C3646: 'noexcept' : unknown override specifier  
```  
  
Also, apparently `-std=c++11` is passed to the compiler, but MSVC does not support such an option.  
  
You should either add Appveyor integration to Sort, or watch https://ci.appveyor.com/project/boostorg/boost/history for the "Update sort from ..." commits and look at the failures there.  
  
Travis integration will probably help too; I can add this for you if you like.

---

## Comment 5

> Username: fjtapia  
> Created at: 2017-11-13 18:30:41 UTC  
> Url: https://github.com/boostorg/sort/issues/17#issuecomment-344013143  

I am trying to adapt the code to msvc-12. But there are two problems  
because the compiler don’t support the keywords noexcept and constexpr.  
  
  
   - The related with noexcept have a easy solution. Delete it in all the  
   functions, and the code run.  
   - But the related with constexpr is a really hard problem. Because  
   flat_stable_sort, block_indirect_sort, sample_sort and  
   parallel_stable_sort, make an extensive use of the metaprogramming and  
   sfinae. And the “old style”, without constexpr, make the metaprogramming  
   much more complex, and I am not sure of can do all.  
  
The parallel Algorithms need too a C++11 support , because use  
futures,threads, atomic variables, and many other thinks defined in the  
C++11 standard. Without full support of them, they can’t compile and run.  
  
Thinking by the positive way, perhaps help to the users to leave this  
version and the next, and embrace the version of 2017  
  
2017-11-11 13:00 GMT+01:00 Peter Dimov <notifications@github.com>:  
  
> msvc-14.1 is now green:  
>  
> https://ci.appveyor.com/project/boostorg/boost/build/1.0.4718-develop  
>  
> but there are failures on msvc-12.0 related to noexcept which the  
> compiler does not support.  
>  
> compile-c-c++ bin.v2\libs\sort\test\test_flat_stable_sort.test\msvc-12.0\debug\optimization-speed\threadapi-win32\threading-multi\test_flat_stable_sort.obj  
> cl : Command line warning D9002 : ignoring unknown option '-std=c++11'  
> test_flat_stable_sort.cpp  
> C:\projects\boost\boost/sort/common/util/algorithm.hpp(87) : error C2143: syntax error : missing ';' before 'const'  
> C:\projects\boost\boost/sort/common/util/algorithm.hpp(87) : error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
> C:\projects\boost\boost/sort/common/util/algorithm.hpp(114) : error C3646: 'noexcept' : unknown override specifier  
> C:\projects\boost\boost/sort/common/util/algorithm.hpp(128) : error C3646: 'noexcept' : unknown override specifier  
>  
> Also, apparently -std=c++11 is passed to the compiler, but MSVC does not  
> support such an option.  
>  
> You should either add Appveyor integration to Sort, or watch  
> https://ci.appveyor.com/project/boostorg/boost/history for the "Update  
> sort from ..." commits and look at the failures there.  
>  
> Travis integration will probably help too; I can add this for you if you  
> like.  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/17#issuecomment-343659860>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ADrumFq0FedBhILstAz_8vZfXw1D-u0eks5s1YxogaJpZM4QOJqK>  
> .  
>

---

## Comment 6

> Username: pdimov  
> Created at: 2017-11-13 18:44:13 UTC  
> Url: https://github.com/boostorg/sort/issues/17#issuecomment-344017034  

It's fine if you don't support msvc-12.0 for the C++11 algorithms, but the Jamfile isn't correct. You're not supposed to add `-std=c++11` to the command line; the choice of what C++ mode to use is made by whoever runs the tests. He may wish to use C++14 or C++17, for instance.  
  
The right way is to add `[ requires cxx11_constexpr ]` (for example) to the tests that require C++11; this will skip them on compilers that don't support `constexpr` according to Boost.Config. (Or you could use `[ requires cxx11_noexcept ]` instead if you like.)  
  
See http://www.boost.org/doc/libs/1_65_1/libs/config/doc/html/boost_config/build_config.html for how to do it.

---

## Comment 7

> Username: fjtapia  
> Created at: 2017-11-13 23:15:42 UTC  
> Url: https://github.com/boostorg/sort/issues/17#issuecomment-344091612  

I changed the Jamfile of the test folder. I think now is correct according  
your instructions. If see anything wrong, please, say me for correct it as  
soon as possible  
Thanks  
  
2017-11-13 19:44 GMT+01:00 Peter Dimov <notifications@github.com>:  
  
> It's fine if you don't support msvc-12.0 for the C++11 algorithms, but the  
> Jamfile isn't correct. You're not supposed to add -std=c++11 to the  
> command line; the choice of what C++ mode to use is made by whoever runs  
> the tests. He may wish to use C++14 or C++17, for instance.  
>  
> The right way is to add [ requires cxx11_constexpr ] (for example) to the  
> tests that require C++11; this will skip them on compilers that don't  
> support constexpr according to Boost.Config. (Or you could use [ requires  
> cxx11_noexcept ] instead if you like.)  
>  
> See http://www.boost.org/doc/libs/1_65_1/libs/config/doc/html/  
> boost_config/build_config.html for how to do it.  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/17#issuecomment-344017034>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ADrumEiKGGRgupVzyeQgbN6AdNUFYb9uks5s2I39gaJpZM4QOJqK>  
> .  
>

---

## Comment 8

> Username: pdimov  
> Created at: 2017-11-13 23:23:10 UTC  
> Url: https://github.com/boostorg/sort/issues/17#issuecomment-344093091  

I've reviewed your commit and I'll fix the Jamfile for you.

---

## Comment 9

> Username: pdimov  
> Created at: 2017-11-14 01:40:41 UTC  
> Url: https://github.com/boostorg/sort/issues/17#issuecomment-344117937  

Done. I also enabled Travis: https://travis-ci.org/boostorg/sort
