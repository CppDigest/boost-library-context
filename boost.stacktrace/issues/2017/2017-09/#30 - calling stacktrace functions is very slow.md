# #30 - calling stacktrace functions is very slow [Closed]

> Username: Ali-Se  
> Created at: 2017-09-26 03:20:19 UTC  
> Updated at: 2018-10-09 18:48:17 UTC  
> Closed at: 2018-10-09 18:48:17 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/30  

I'm developing a solution where the call stack is stored in XML file, so I call the name(), source_file(), and source_line() functions for each frame, I takes about 45 seconds to complete the loop on stack depth of about 70 levels.

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-09-26 17:41:27 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/30#issuecomment-332277718  

What compiler and OS are you using?

---

## Comment 2

> Username: Ali-Se  
> Created at: 2017-09-27 12:11:04 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/30#issuecomment-332500708  

Windows 10 + Visual Studio 2015 update 4

---

## Comment 3

> Username: apolukhin  
> Created at: 2017-09-27 20:38:23 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/30#issuecomment-332647898  

Try defining the BOOST_STACKTRACE_USE_WINDBG_CACHED macro or link with boost_stacktrace_windbg_cached (see http://www.boost.org/doc/libs/master/doc/html/stacktrace/configuration_and_build.html for more info).  
  
Please report back the results.

---

## Comment 4

> Username: Ali-Se  
> Created at: 2017-09-28 05:53:28 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/30#issuecomment-332734900  

I got this error message:  
../boost/stacktrace/detail/frame_msvc.ipp(167): fatal error C1189: #error:  Your compiler does not support C++11 thread_local storage. It's impossible to build with BOOST_STACKTRACE_USE_WINDBG_CACHED.

---

## Comment 5

> Username: apolukhin  
> Created at: 2018-10-09 18:48:17 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/30#issuecomment-428306850  

For compilers that do not support `thread_local` storage there's no good library way to speed up the stacktrace decoding.  
  
However, you could cache decoded strings. Make an `unordered_map<frame, std::string>`. That should help a lot.  
  
P.S: I'll try to implement that stacktrace caching some day. If it fits well as a generic Boost library solution - I'll merge it into the Boost.Stacktrace library.
