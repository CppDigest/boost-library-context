# #1149 - Error_code posted from async_read is invalid and causing a segmentation fault in the context of error_category [Closed]

> Username: TheQuantumPhysicist  
> Created at: 2018-06-03 16:18:19 UTC  
> Updated at: 2018-06-05 17:58:12 UTC  
> Closed at: 2018-06-05 17:50:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1149  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
### Version of Beast  
Boost 1.67  
  
### Steps necessary to reproduce the problem  
In my case, I would:  
  
1. create the server object with an acceptor  
2. create the client, and connect to the server, which in turn creates a session object that contains the connected socket  
3. do some communication  
4. issues an async_read finally on the server  
5. close the client connection  
6. when async_read returns with an error, the error code value (`m_val`) is gigantic (garbage value apparently) and causes a segfault when trying to retrieve the message.  
  
I wasn't able to reproduce the problem in the example of async ssl websocket server/client, although this is what I based my work on (with the change that I used an io_service pool for the sever because I'm gonna use high parallelism in my application, so I want io_service caching to be optimum as I'm gonna use a multi-cpu server for horizontal scaling). I'm not sure what the exact circumstances are that create this specific error, however, given that I tested that the server session object is valid and alive (and validated that's the case through the gdb debugger), there's no reason I'm aware of that makes boost::asio/beast produce an invalid error code, and hence I created this ticket because it looks like there's a bug.  
  
I'm open to sending you the complete source code of my project to your email. It's still in its infancy.  
  
Also if there's something I can check myself and test, please let me know. Otherwise, sending you the source code sounds like a good idea.   
  
### All relevant compiler information  
  
I tried with both gcc 5.4 and 7.3 Both produce the problem. This is on Ubuntu 16.04 LTS and 18.04 LTS. Although I have to say that the debugging information on gcc 5.4 was much better.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-06-03 16:39:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1149#issuecomment-394174507  

This looks like a problem with how you are linking to Boost.System.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-06-03 16:46:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1149#issuecomment-394175014  

I think what is happening is you are using a newer `<boost/system/error_code.hpp>` with an older version of the static or dynamic library. The layout of `error_category` changed in Boost 1.65.0.

---

## Comment 3

> Username: TheQuantumPhysicist  
> Created at: 2018-06-03 17:07:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1149#issuecomment-394176225  

You may be right, and this is a huge problem when working with custom libraries in Linux and probably CMake takes a part of the blame. This is how I link to boost:  
  
```  
set(Boost_NO_SYSTEM_PATHS ON)  
set(BOOST_ROOT "/path/to/boost/boost/")  
set(BOOST_LIBRARYDIR "/path/to/boost/boost/stage/lib/")  
set(BOOST_INCLUDEDIR "/path/to/boost/boost/")  
```  
  
I went through my whole project and changed all includes from <boost/...> to "boost/...". I can imagine that the includes themselves have other includes inside them that seek the boost version from the system, not from where I tell it to look. Do you know of any solution that doesn't involve completely uninstalling boost from my system? The problem with uninstalling boost from my system is that it'll take down many other apps/packages that depend on it.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-06-03 17:45:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1149#issuecomment-394178763  

I'm not an expert on the build systems, and on Linux even less so. However, I have a high confidence that the problem can be resolved entirely by adjusting your build script, without the necessity of reinstalling Boost (assuming that Boost is correctly installed on your system). As a last resort, you could always create your own private instance of boost in your home directory, build the libraries yourself, and adjust your build scripts to point to the includes and libraries in your private instance.

---

## Comment 5

> Username: TheQuantumPhysicist  
> Created at: 2018-06-03 17:51:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1149#issuecomment-394179144  

Actually, I'm using a copy I built myself in my home directory. But it looks like that g++ is ignoring it, if your assumption that the wrong includes are the cause of the problem.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-06-03 18:24:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1149#issuecomment-394181208  

Have you tried building the Beast examples using b2?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-06-03 18:25:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1149#issuecomment-394181238  

Oh wait you said the examples worked for you. Definitely a problem with your build script then.

---

## Comment 8

> Username: TheQuantumPhysicist  
> Created at: 2018-06-03 18:27:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1149#issuecomment-394181424  

Yes, actually the examples work with no issue. There's no build script. It's just cmake with the lines that I presented in the question + these two lines:  
  
```  
include_directories(${Boost_INCLUDE_DIRS})  
LINK_DIRECTORIES(${BOOST_LIBRARYDIR})  
```  
Am I missing something?

---

## Comment 9

> Username: TheQuantumPhysicist  
> Created at: 2018-06-03 19:21:27 UTC  
> Updated at: 2018-06-03 19:21:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1149#issuecomment-394184873  

OK. Now I'm starting to be certain that this is not a linking problem. The following is the verbose build output: https://pastebin.com/0rabEiFJ  
  
You'll see there that `/usr/lib/` is exclusively used for OpenSSL. I also moves `/usr/include/boost/` to `/usr/include/boost1/` temporarily, to see the outcome, and the problem still happened.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-06-03 20:25:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1149#issuecomment-394188764  

Well, if the examples work for you then starting from a working example just modify it one step at a time to be like your program which malfunctions and figure out where the malfunction takes place?

---

## Comment 11

> Username: vinniefalco  
> Created at: 2018-06-03 20:25:52 UTC  
> Updated at: 2018-06-03 20:26:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1149#issuecomment-394188817  

You can email me the source code (I will treat it as being under NDA) to my email if you would like, perhaps it is a bug in your code. Try to send the smallest amount of code which is complete and exhibits the bug.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2018-06-05 17:52:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1149#issuecomment-394801648  

Did this get sorted?

---

## Comment 13

> Username: TheQuantumPhysicist  
> Created at: 2018-06-05 17:58:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1149#issuecomment-394803469  

I just finished writing an email to you explaining how it turned out. Apparently I was keeping sessions alive although they failed to connect, which led to garbage values everywhere. In addition to this, I did many multithreaded and memcheck improvement through valgrind. As it's undefined behavior, I can only say that it doesn't appear anymore and stress tests on my server are stable now. Hopefully it's over and will never appear again.
