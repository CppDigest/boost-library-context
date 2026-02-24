# #402 - Cygwin 64 bit, asio library, unix socket & Openroad [Open]

> Username: dm3xxx  
> Created at: 2022-09-18 04:52:16 UTC  
> Updated at: 2022-11-15 22:21:37 UTC  
> Url: https://github.com/boostorg/asio/issues/402  

I'm trying to build boost v1.76 on Cygwin-64 for Openroad project.  It comes to a point, obvious to me, openroad require the asio library from boost.  
  
However, base on the boost doc html, the web states only Win32 using Cygwin.  I'm using the Cygwin 64-bit, and I do NOT want the windows 32 socket, neither.  
  
Unfortunately, I couldn't find anything to tell me how to build boost on Cygwin 64-bit with unix socket. I only know people use 1.66 unix socket just fine.  
  
Even though my 1.76 build was successful, once it goes to openroad build, just simply failed and required the win 32 socket. Since cygwin only can provide up to 1.66 atm, just wonder if anyone ever successfully built version more advance that cygwin can provide?  If so how? Or it's simply impossible?  
  
  
In case anyone doesn't know what i'm talking about openroad and asio  
  
https://github.com/The-OpenROAD-Project  
https://www.boost.org/doc/libs/1_76_0/doc/html/boost_asio/using.html  
  
Thanks for help
