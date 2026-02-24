# #55 - clang supported? [Closed]

> Username: cryptocode  
> Created at: 2018-05-03 18:20:29 UTC  
> Updated at: 2018-05-14 10:39:06 UTC  
> Closed at: 2018-05-14 09:41:04 UTC  
> Url: https://github.com/boostorg/interprocess/issues/55  

clang (macos/linux) is not listed as a tested compiler, but is the level of support known? Any known issues? Thanks for any input.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2018-05-14 09:41:04 UTC  
> Url: https://github.com/boostorg/interprocess/issues/55#issuecomment-388757892  

It's tested on the regression tests and seems to work. See:  
  
https://www.boost.org/development/tests/master/developer/interprocess.html  
  
Clang is being tested on Linux, FreeBsd and Mac.

---

## Comment 2

> Username: cryptocode  
> Created at: 2018-05-14 10:38:20 UTC  
> Updated at: 2018-05-14 10:39:06 UTC  
> Url: https://github.com/boostorg/interprocess/issues/55#issuecomment-388773188  

@igaztanaga Thanks - the docs over supported compilers doesn't mention clang, so this is good to know!   
  
I've been have some issues with clang on macOS, but I may have been running into one of the bugs listed here:https://svn.boost.org/trac10/query?status=!closed&component=interprocess&desc=1&order=id    
  
Are these being worked on, or is the list outdated? Of the issues I'm having is deadlocks/hangs if a participating processes crashes (using named segments and condition variables).
