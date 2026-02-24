# #42 Fix bug ticket 11128 [Closed]

> Username: Bjoe  
> Created at: 2015-03-20 00:01:19 UTC  
> Updated at: 2016-05-27 08:43:00 UTC  
> Closed at: 2016-05-27 08:43:00 UTC  
> Url: https://github.com/boostorg/test/pull/42  

Fix build error on QNX/BB10 crosscompiling

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2015-03-21 15:11:30 UTC  
> Url: https://github.com/boostorg/test/pull/42#issuecomment-84368624  

Hi,  
  
We have some QNX runners. Can you tell me which one is failing?

---

## Comment 2

> Username: Bjoe  
> Created_at: 2015-03-22 15:12:50 UTC  
> Updated_at: 2015-03-22 15:13:34 UTC  
> Url: https://github.com/boostorg/test/pull/42#issuecomment-84628129  

Hi,  
  
QNX runners, you mean this one?  
http://www.boost.org/development/tests/develop/NA-QNX650-SP1-ARM.html  
  
I crosscompile it for Blackberry 10.  
Here some facts:  
Target platform: Blackberry 10 (Based on QNX 6.5.0)  
Compiler: gcc invoked via QCC.  Version 4.8.3.   
Using libstdc++ library from Dinkum, not from GNU!  
  
Here CXX flags:  
-V4.8.3,gcc_ntoarmv7le_cpp -Y_cpp  
  
Without (-std=c++0x") C++11. It's not possible on Blackberry 10.  
  
I build Boost with:  
--user-config=blackberry-config.jam --layout=versioned --build-type=complete toolset=qcc target-os=qnxnto address-model=32 architecture=arm --without-python  
  
blackberry-config.jam:  
  
<pre>  
using qcc  
# Version  
    :   
# C++-compile-command  
    : $(QNX_HOST)/usr/bin/QCC -V4.8.3,gcc_ntoarmv7le_cpp  
# Compiler options  
    : <compileflags>"-D__QNX__"  
        <compileflags>"-D__QNXNTO__"  
    # Add -D__USE_ISOC99 for rounding control mechanism (boost/numeric/interval/hw_rounding.hpp)  
        <compileflags>"-D__USE_ISOC99"  
        <compileflags>"-I$(QNX_TARGET)/usr/include"  
    # Prevent this from being output: "note: the mangling of 'va_list' has changed in GCC 4.4"  
        <compileflags>"-Wno-psabi"  
    # Add -fPIC to link without error to LibTestNativeLibTuTest.o (for statechart tests)  
        <compileflags>"-fPIC"  
    # Requested by BBM team  
        <compileflags>"-fno-strict-aliasing"  
    # Functions like sync_val_compare_and_swap and sync_fetch_and_add aren't available  
        <compileflags>"-DBOOST_SP_NO_SYNC"  
      
    # syslog aren't available  
        <compileflags>"-DBOOST_LOG_WITHOUT_SYSLOG"  
        <linkflags>-V$(dashVOptLink)  
        <linkflags>"-L$(QNX_TARGET)/$(linkArchDir)/usr/lib -L$(QNX_TARGET)/$(linkArchDir)/lib"  
    ;  
</pre>  
  
See also https://github.com/blackberry/Boost/blob/1_48_0-gnu/blackberry-config.jam  
  
I think the problem is the Dinkum library. It is possible to change this on the QNX runners?

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2015-03-23 13:26:33 UTC  
> Url: https://github.com/boostorg/test/pull/42#issuecomment-84996337  

I do not have any access to this runner, so I would say "no". I wanted a way of ensuring there is no regression in the future, but apparently this would not be possible.

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2016-05-27 08:43:00 UTC  
> Url: https://github.com/boostorg/test/pull/42#issuecomment-222092999  

Currently under testing, should be in 1.62. Thanks.

---
