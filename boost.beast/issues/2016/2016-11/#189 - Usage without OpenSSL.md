# #189 - Usage without OpenSSL [Closed]

> Username: rberlich  
> Created at: 2016-11-15 14:43:26 UTC  
> Updated at: 2016-11-15 16:42:23 UTC  
> Closed at: 2016-11-15 16:42:23 UTC  
> Url: https://github.com/boostorg/beast/issues/189  

Hi there, thanks for this excellent library! I would like to use it without OpenSSL, though, as this introduces another dependency. My use-case does not involve communication over unprotected network connections, hence all I need is unencrypted communication. Compiling the package does include OpenSSL on my Mac, but a "grep -R" does not show any associated headers. Presumably these are included indirectly via Boost.ASIO ? Kind Regards, Beet

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-11-15 15:38:26 UTC  
> Url: https://github.com/boostorg/beast/issues/189#issuecomment-260675080  

@rberlich Beast is a header-only library, you don't need to build it in order to use it in your projects (but you do need the Boost libraries, some of which require compilation e.g. Boost.System). You don't need OpenSSL to use Beast either, unless you want HTTP or WebSocket over TLS connections obviously :)  
  
The CMake and bjam build scripts that come with Beast are used to compile the tests, benchmarks, and example programs - these are not necessary to use the library in your program. A couple of the examples use OpenSSL:  
https://github.com/vinniefalco/Beast/tree/master/examples/ssl  
  
These OpenSSL examples are tucked away in their own directory. Furthermore, they will only be compiled if you have OpenSSL on your system, see:  
https://github.com/vinniefalco/Beast/blob/master/CMakeLists.txt#L108  
  
Hope this helps!

---

## Comment 2

> Username: rberlich  
> Created at: 2016-11-15 16:33:31 UTC  
> Url: https://github.com/boostorg/beast/issues/189#issuecomment-260692218  

Vinnie, thanks.   
  
I am aware of the Boost requirement and use many of the Boost libs in my application already. I was just afraid of having to add yet another "external" library, even in the non-encrypted case (as is e.g. the case for https://github.com/eidheim/Simple-WebSocket-Server ). But this way (OpenSSL fully optional in Beast) it is perfect, and I'm looking forward to the inclusion of Beast into Boost (hopefully!).   
  
Thanks again and Kind Regards, Beet

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-11-15 16:42:23 UTC  
> Url: https://github.com/boostorg/beast/issues/189#issuecomment-260694879  

Good luck and if you have any trouble integrating Beast feel free to open another issue! Thanks!
