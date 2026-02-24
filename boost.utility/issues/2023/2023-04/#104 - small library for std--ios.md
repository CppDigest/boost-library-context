# #104 - small library for std::ios [Closed]

> Username: gpeterhoff  
> Created at: 2023-04-27 00:17:13 UTC  
> Updated at: 2023-04-27 10:41:33 UTC  
> Closed at: 2023-04-27 10:41:33 UTC  
> Url: https://github.com/boostorg/utility/issues/104  

Hello,  
I have developed a small library for accessing std::ios. This saves manual handling of e.g. ios::flags etc.  
Could you please include this in boost?  
Is boost/utility the right location?  
  
[ios.hpp.txt](https://github.com/boostorg/utility/files/11338238/ios.hpp.txt)  
  
thx  
Gero

---

## Comment 1

> Username: Lastique  
> Created at: 2023-04-27 10:41:33 UTC  
> Url: https://github.com/boostorg/utility/issues/104#issuecomment-1525442165  

There is already Boost.IO, in particular [`io_state.hpp`](https://github.com/boostorg/io/blob/83e927f998e83a29be8fb033389110affe8787f5/include/boost/io/ios_state.hpp).  
  
Also, for future reference, code submissions should follow [Boost coding guidelines](https://www.boost.org/development/requirements.html#Design_and_Programming), in particular, don't use tabs. Submissions should include documentation and tests. Among other things, docs should include motivation.
