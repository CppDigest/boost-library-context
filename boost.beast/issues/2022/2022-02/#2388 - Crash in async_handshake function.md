# #2388 - Crash in async_handshake function [Closed]

> Username: alesm2015  
> Created at: 2022-02-08 19:25:02 UTC  
> Updated at: 2022-09-24 05:08:46 UTC  
> Closed at: 2022-09-24 05:08:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2388  

This is the issue, which was already addressed in boost-users@lists.boost.org list, that the function async_handshake crashes.  
I tried to prepare the smallest possible code, where the function still crashes. I'm using VS 2022 and the latest BOOST from github. Issue can be reproduced with 1.78.0 version as well. Also using latest OpenSSL from github, but it was reproduced with 3.0.1.  
Code is compiled in x64, /std:c++20.   
The sample code is located at: https://github.com/alesm2015/Boost-TLS-Server.git  
Only the location of BOOST and OpenSSL needs to be properly configured and code will compile.  Also additional TLS client is required in order to reproduce crash.  
However the most interesting thing is, that the code works well if I simply remove two source files (binance_exchange.cpp and exchange.cpp) from compilation, despite those sources are never called, they are only compiled in this example. It looks, that something corrupts the heap memory. I double check mine sources and everything looks fine to me.

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-02-08 19:49:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2388#issuecomment-1032999164  

Thank you I'm on it.

---

## Comment 2

> Username: alesm2015  
> Created at: 2022-02-08 22:20:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2388#issuecomment-1033120950  

I added simplied client to the same sources. Now the issue is simple to reproduce:  
1. Compile both sources  
2. Start server  
3. Start client  
As soon as client tries to connect to server, the server will crash.

---

## Comment 3

> Username: madmongo1  
> Created at: 2022-02-09 14:01:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2388#issuecomment-1033791619  

I've compiled on linux and run. It doesn't do much but it doesn't crash.  
Now trying on Windows.

---

## Comment 4

> Username: alesm2015  
> Created at: 2022-02-10 08:52:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2388#issuecomment-1034651633  

Thank you for your efforts. I didn't test the code on Linux, only on Visual Studio 2022. The code is intentionally as simple as possible, helping to focus to the issue only. I tested this code on two different computers and it crashed, on both each time. Using the same visual studio on both machines.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-02-10 15:44:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2388#issuecomment-1035074277  

is that the release version of 2022 or a preview? try 2019, maybe there's a compiler bug. I never upgrade to the latest Visual Studio until it has been out for at least one year.

---

## Comment 6

> Username: alesm2015  
> Created at: 2022-02-10 16:21:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2388#issuecomment-1035119838  

Tested with Visual Studio 2019 (version 16.11.10). I changed platform toolset back to v142. I also used OpenSSL v. 3.0.1 from https://kb.firedaemon.com/support/solutions/articles/4000121705, to avoid recompiling entire openssl sorces for VS 2019.  
The code still crash on the exactly the same location.

---

## Comment 7

> Username: madmongo1  
> Created at: 2022-02-10 17:41:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2388#issuecomment-1035221699  

OK, I'll find it.

---

## Comment 8

> Username: alesm2015  
> Created at: 2022-02-10 19:14:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2388#issuecomment-1035368000  

Not sure if this helps, but I think, the location where things starts rolling into wrong direction starts:  
  
1. basic_stream.hpp  
in operator() in line: state().timer.async_wait(.......  
  
2. async_base.hpp  
in get_executor() const noexcept  
  
3. associated_executor.hpp  
in get_associated_executor(const T& t, const Executor& ex ....  
the value in ex looks corrupted

---

## Comment 9

> Username: vinniefalco  
> Created at: 2022-06-14 17:38:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2388#issuecomment-1155502171  

Is this resolved?
