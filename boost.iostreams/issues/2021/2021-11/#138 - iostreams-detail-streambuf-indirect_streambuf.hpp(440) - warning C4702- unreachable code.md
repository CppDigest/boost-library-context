# #138 - iostreams\detail\streambuf\indirect_streambuf.hpp(440) : warning C4702: unreachable code [Open]

> Username: pawelsopensource  
> Created at: 2021-11-25 09:56:38 UTC  
> Updated at: 2023-07-07 22:34:43 UTC  
> Url: https://github.com/boostorg/iostreams/issues/138  

hi,  
i'm compiling my .dll code for release without optimization (/W3 /WX /MD /Od) and msvc reports warning (error in my case):  
  
boost-1.77.0\include\boost\iostreams\detail\streambuf\indirect_streambuf.hpp(440) : error C2220: the following warning is treated as an error  
boost-1.77.0\include\boost\iostreams\detail\streambuf\indirect_streambuf.hpp(440) : warning C4702: unreachable code  
  
as a local workaround i've added following pragma to disable_warnings.hpp  
  
pragma warning(disable:4702)    // Unreachable code

---

## Comment 1

> Username: mclow  
> Created at: 2021-11-30 04:47:22 UTC  
> Url: https://github.com/boostorg/iostreams/issues/138#issuecomment-982282052  

I'm not seeing how that code is unreachable.  Can you post more info?  
```  
438:    if (shared_buffer() && gptr() != 0) {  
439:        obj().seek(static_cast<off_type>(gptr() - egptr()), BOOST_IOS::cur, BOOST_IOS::in, next_);  
440:        setg(0, 0, 0);  
441:    }  
442:    if (output_buffered())  
443:        setp(out().begin(), out().end());  
444:    else  
445:        setp(0, 0);  
```

---

## Comment 2

> Username: balagansky-work  
> Created at: 2023-07-07 00:16:58 UTC  
> Updated at: 2023-07-07 00:39:21 UTC  
> Url: https://github.com/boostorg/iostreams/issues/138#issuecomment-1624462610  

This code is unreachable under at least some versions of MSVC (14.36 but not 14.35) if shared_buffer() returns false, only with certain combinations of settings.  
  
A minimal repro with MSVC 14.36 is simply:  
  
```  
#include <boost/iostreams/filtering_stream.hpp>  
boost::iostreams::filtering_ostream s;  
```  
  
Starting from an empty project's Release configuration, only the following changes from the default project settings are needed:  
* boost include dir is specified  
* Warning Level = 4  
* Whole Program Optimization = No  
  
Since filtering_ostream's category/mode is 'output', the condition on line 438 will always be false, and both 439 and 440 would be unreachable.  
  
Curiously, the warning is on line 440 (setg), not line 439 (immediately after the condition). I wonder if the internal mechanism doesn't have directly to do with shared_buffer always returning false due to the trait checks, but rather something about line 439 - perhaps it's seen by the compiler as always throwing, or something like that.  
  
We also see little choice but to use the pragma workaround mentioned by OP.

---

## Comment 3

> Username: balagansky-work  
> Created at: 2023-07-07 22:34:43 UTC  
> Url: https://github.com/boostorg/iostreams/issues/138#issuecomment-1626320799  

Submitted a VS feedback item here: https://developercommunity.visualstudio.com/t/Unexpected-C4702-Unreachable-code-in-boo/10410775
