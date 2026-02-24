# #46 - win + pure clang (not clang-cl) build error [Closed]

> Username: egorpugin  
> Created at: 2020-02-07 12:30:32 UTC  
> Updated at: 2020-09-25 21:06:05 UTC  
> Closed at: 2020-09-25 21:06:04 UTC  
> Url: https://github.com/boostorg/mpl/issues/46  

Error:  
```  
d:\dev\cppan2\client2\.sw\rsp>8169297309854606892.bat -w  
[sw.client.manager-0.4.0]/src/sw/manager/source.cpp  
In file included from D:/dev/cppan2/client2/src/sw/manager/source.cpp:12:  
In file included from D:/dev/cppan2/client2/.s/pkg/07/63/651e/src/sdir/src/date_time/include\primitives/date_time.h:9:  
In file included from D:/dev/cppan2/client2/.s/pkg/d5/f9/3578/src/sdir/include\boost/date_time/posix_time/posix_time.hpp:24:  
In file included from D:/dev/cppan2/client2/.s/pkg/d5/f9/3578/src/sdir/include\boost/date_time/posix_time/time_formatters.hpp:12:  
In file included from D:/dev/cppan2/client2/.s/pkg/d5/f9/3578/src/sdir/include\boost/date_time/gregorian/gregorian.hpp:31:  
In file included from D:/dev/cppan2/client2/.s/pkg/d5/f9/3578/src/sdir/include\boost/date_time/gregorian/gregorian_io.hpp:16:  
In file included from D:/dev/cppan2/client2/.s/pkg/d5/f9/3578/src/sdir/include\boost/date_time/date_facet.hpp:17:  
In file included from D:/dev/cppan2/client2/.s/pkg/f7/7f/7cb2/src/sdir/include\boost/algorithm/string/replace.hpp:16:  
In file included from D:/dev/cppan2/client2/.s/pkg/7d/f2/028d/src/sdir/include\boost/range/iterator_range_core.hpp:27:  
In file included from D:/dev/cppan2/client2/.s/pkg/c0/af/5f1e/src/sdir/include\boost/iterator/iterator_facade.hpp:11:  
In file included from D:/dev/cppan2/client2/.s/pkg/c0/af/5f1e/src/sdir/include\boost/iterator/interoperable.hpp:11:  
In file included from D:/dev/cppan2/client2/.s/pkg/3d/f2/f30f/src/sdir/include\boost/mpl/or.hpp:43:  
D:/dev/cppan2/client2/.s/pkg/3d/f2/f30f/src/sdir/include\boost/mpl/aux_/include_preprocessed.hpp:37:13: fatal error: 'boost/mpl/aux_/preprocessed/plain/||.hpp' file not found  
#   include BOOST_PP_STRINGIZE(boost/mpl/aux_/preprocessed/AUX778076_PREPROCESSED_HEADER)  
            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
D:/dev/cppan2/client2/.s/pkg/ba/e0/f079/src/sdir/include\boost/preprocessor/stringize.hpp:28:38: note: expanded from macro 'BOOST_PP_STRINGIZE'  
#    define BOOST_PP_STRINGIZE(text) BOOST_PP_STRINGIZE_I(text)  
                                     ^~~~~~~~~~~~~~~~~~~~~~~~~~  
D:/dev/cppan2/client2/.s/pkg/ba/e0/f079/src/sdir/include\boost/preprocessor/stringize.hpp:32:36: note: expanded from macro 'BOOST_PP_STRINGIZE_I'  
# define BOOST_PP_STRINGIZE_I(...) #__VA_ARGS__  
                                   ^~~~~~~~~~~~  
<scratch space>:104:1: note: expanded from here  
"boost/mpl/aux_/preprocessed/plain/||.hpp"  
^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
1 error generated.  
```  
  
This is caused by following line in files: `and.hpp`, `bitand.hpp`, `or.hpp`, `bitor.hpp`:  
```  
#if defined(_MSC_VER) && !defined(__clang__)  
```  
  
This is not true anymore today. Commenting out the second conditions helps.  
Pure clang defines `_MSC_VER` plus `__clang__` on windows.

---

## Comment 1

> Username: Milerius  
> Created at: 2020-03-31 02:46:04 UTC  
> Url: https://github.com/boostorg/mpl/issues/46#issuecomment-606365886  

This need to be solved urgently

---

## Comment 2

> Username: Kojoley  
> Created at: 2020-04-03 16:15:07 UTC  
> Url: https://github.com/boostorg/mpl/issues/46#issuecomment-608530757  

@Milerius MPL has no dedicated maintainer and the issue is not a regression, you best bet is to develop a fix and file it via pull request

---

## Comment 3

> Username: eldiener  
> Created at: 2020-09-25 19:40:00 UTC  
> Url: https://github.com/boostorg/mpl/issues/46#issuecomment-699118263  

This is for clang on Windows targeting vc++.  I have clang on Windows targeting vc++ and am able to test this using a the clang-win.jam file, but I need a simple example to test this issue. If just run the mpl tests with clang-win and the latest clang-10.0 on Windows targeting vc++ I see no failures in the tests. Please give a command line and some code where this bug is manifest. Whatever is the code you give in your initial post above I have no idea what is being compiled and  what trhe clang command line is.

---

## Comment 4

> Username: egorpugin  
> Created at: 2020-09-25 21:06:04 UTC  
> Url: https://github.com/boostorg/mpl/issues/46#issuecomment-699155200  

Seems this was fixed with newer boost.  
The fix is in this commit https://github.com/matbech/mpl/commit/d5fc194ce164301250f6feea9f194ccffdafb4f0
