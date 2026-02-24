# #1895 - FATAL ERROR "WinSocket.h" already included [Closed]

> Username: FabrizioDys  
> Created at: 2020-04-08 11:13:41 UTC  
> Updated at: 2025-07-16 10:09:29 UTC  
> Closed at: 2020-04-24 17:37:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1895  

Hi!  
  
I'm trying to use the HTTP request methods provided by this [page:](https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/quick_start.html)  
  
I am actually implementing this to an already done project ( MEPP2 ) that uses graph and iterator components, I generated through CMake the dependencies with the boost library, and I'm "forced" to use version 1.67. The problem I have when I include   
`#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>`  
  
these ones is "Fatal Error: Winsocket.h" already included. Can anyone help me out? I can send some more informations if needed. Thank you :)  
  
PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
If you rather keep your project secret, feel free  
to email the author at `<vinnie.falco@gmail.com>`; any  
private correspondence is treated as confidential.  
  
When reporting a bug please include the following:  
  
### Version of Beast  
  
You can find the version number in the file `<boost/beast/version.hpp>`,  
or by using the command "git log -1" in the local Beast repository  
directory.  
  
### Steps necessary to reproduce the problem  
  
A small compiling program is the best. If your code is  
public, you can provide a link to the repository.  
  
### All relevant compiler information  
  
If you are unable to compile please include the type and  
version of compiler you are using as well as all compiler  
output including the error message, file, and line numbers  
involved.  
  
The more information you provide the sooner your issue  
can get resolved!

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-04-08 11:57:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1895#issuecomment-610915708  

Hi @FabrizioDys ,  
  
Are you able to share with me a compilation unit which exhibits the problem, perhaps by a _gist_ or by sharing a link to github or godbolt?  
  
R

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-04-08 12:26:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1895#issuecomment-610928721  

https://stackoverflow.com/questions/9750344/boostasio-winsock-and-winsock-2-compatibility-issue

---

## Comment 3

> Username: FabrizioDys  
> Created at: 2020-04-08 12:41:56 UTC  
> Updated at: 2020-04-08 12:42:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1895#issuecomment-610935844  

Hi @madmongo1 , [HERE](https://github.com/MEPP-team/MEPP2) is the link for MEPP2, I'm a student who's collaborating with one of the developers so the product was already as you see it. The thing that I am doing now is to add another piece of code ( a filter ) for retrieving a file from internet, so, since they were already using Boost libraries, I thought I could use the asio and beast components to achieve the result. The program is kind of complicated so, following @vinniefalco 's suggestion I am still trying to find where "#include <windows.h>" is to understand how to resolve this conflict.  
Do you think it's possible to edit this part of code?  
From "socket_types.hpp"  
  
> # if defined(_WINSOCKAPI_) && !defined(_WINSOCK2API_)  
> #  error WinSock.h has already been included  
> # endif // defined(_WINSOCKAPI_) && !defined(_WINSOCK2API_)

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-04-08 13:02:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1895#issuecomment-610946336  

Ho @FabrizioDys ,  
  
I think a better strategy would be to ensure that `#include <boost/asio.hpp>` is the first include in any compilation unit that needs it.  
  
If you've created a project from scratch in Visual Studio, you might find that the precompiled header file `StdAfx.h` includes winsock?

---

## Comment 5

> Username: FabrizioDys  
> Created at: 2020-04-09 09:35:47 UTC  
> Updated at: 2020-04-09 09:36:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1895#issuecomment-611432624  

Hi again.  
  
I looked through the entire solution and there is no "windows.h" included, nor "StdAfx.h", I followed one of the answers of the stackoverflow page @vinniefalco linked me:  
  
instead of including the boost headers I needed I wrote something like this:  
  
> #ifdef _WIN32  
> #ifdef USE_ASIO  
> //     Set the proper SDK version before including boost/Asio  
> #include <SDKDDKVer.h>  
> //     Note boost/ASIO includes Windows.h.  
> #include <boost/asio.hpp>  
> #include <boost/beast/core.hpp>  
> #include <boost/beast/http.hpp>  
> #include <boost/beast/version.hpp>  
> #include <boost/asio/connect.hpp>  
> #include <boost/asio/ip/tcp.hpp>  
> #else //  USE_ASIO  
> #include <Windows.h>  
> #endif //  USE_ASIO  
> #else  // _WIN32  
> #ifdef USE_ASIO  
> #include <boost/asio.hpp>  
> #include <boost/beast/core.hpp>  
> #include <boost/beast/http.hpp>  
> #include <boost/beast/version.hpp>  
> #include <boost/asio/connect.hpp>  
> #include <boost/asio/ip/tcp.hpp>  
> #endif // USE_ASIO  
> #endif //_WIN32  
>   
  
and the error concerning the "WinSock.h" disappeared.  
Then I tried to do something like this:  
  
`using tcp = boost::asio::ip::tcp;  
namespace http = boost::beast::http;`  
  
and I had the following errors:  
  
> asio:  not a member of boost  
> ip: not a member of boost  
> asio: the symbol on the left of '::' must be a type  
  
and going on for tcp, beast and http too.  
Do you have any idea what's happening?

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-04-09 11:22:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1895#issuecomment-611476015  

It’s difficult to say without having code available to reproduce the problem.  
  
Can you share a minimal project with me on github or godbolt that reproduces the problem?

---

## Comment 7

> Username: MasterBe  
> Created at: 2024-04-25 12:56:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1895#issuecomment-2077118367  

This is still a problem for me

---

## Comment 8

> Username: ashtum  
> Created at: 2024-04-25 13:08:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1895#issuecomment-2077142824  

> This is still a problem for me  
  
Could you please provide a minimal reproducible example?

---

## Comment 9

> Username: jjYBdx4IL  
> Created at: 2024-04-30 20:12:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1895#issuecomment-2087044232  

`#define WIN32_LEAN_AND_MEAN` before windows.h include solves the issue for me, which probably isn't optimal.

---

## Comment 10

> Username: wkingnet  
> Created at: 2025-07-16 06:50:26 UTC  
> Updated at: 2025-07-16 10:09:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1895#issuecomment-3077132510  

`#define WIN32_LEAN_AND_MEAN` not recommended, it will destroy other functions.  
  
try  
```  
#include <winsock2.h>  
#include <windows.h>  
...  
#include <boost/beast/core.hpp>  
#include <boost/asio/ssl.hpp>  
```  
  
  
a minimal reproducible example in visual studio:  
```  
#include <windows.h>  
#include <boost/beast/core.hpp>  
int main(int argc, wchar_t* argv[], wchar_t* envp[]) {}  
```  
Can't believe it, right? Only `#include <windows.h>` and `#include <boost/beast/core.hpp>` are needed, no code is needed, and error will occur. Must be a visual studio environment so that `#include <windows.h>`.
