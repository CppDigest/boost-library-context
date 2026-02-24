# #151 - ASIO support for WebAssembly?  Any ETA? [Closed]

> Username: DragonOsman  
> Created at: 2018-09-29 12:41:25 UTC  
> Updated at: 2020-12-30 00:53:47 UTC  
> Closed at: 2020-12-30 00:53:45 UTC  
> Url: https://github.com/boostorg/asio/issues/151  

Hi everyone.  
  
I want to ask if there're any plans for and/or ETA for ASIO supporting WebAssembly.  I have a web server application I wrote using sync server and client examples from the Beast library that I wanted to try porting to WebAssembly, but I was told at the Beast GitHub that ASIO doesn't WebAssembly.     
  
I'll show my source code here for reference, in case someone wants to see it (I have it all on Gist):  
C++: https://gist.github.com/DragonOsman/d00ea7ec3d49c128ad7789293f156a09  
CSS: https://gist.github.com/DragonOsman/3f3ba56ffd58476c6defaea95b066dc2  
HTML: https://gist.github.com/DragonOsman/506bd2bae1dfe4c03a4bbe2a6d830324  
JavaScript: https://gist.github.com/DragonOsman/c6e8fb15343544e662f474c5a526d1c2  
  
The C++ code also declares two environment variables that are needed on the front-end side.  It sends them over as needed.

---

## Comment 1

> Username: DragonOsman  
> Created at: 2018-11-20 14:18:41 UTC  
> Url: https://github.com/boostorg/asio/issues/151#issuecomment-440288804  

Update: there's an [official Emscripten port](https://github.com/emscripten-ports/asio) of ASIO.  May need to verify version and see if it's the latest version.  If it's not, then I want to know how to roll out my own ASIO port locally (I might propose for it to be included in the official port as well if it's good enough).

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 00:53:44 UTC  
> Url: https://github.com/boostorg/asio/issues/151#issuecomment-752290005  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#643](https://github.com/chriskohlhoff/asio/issues/643).
