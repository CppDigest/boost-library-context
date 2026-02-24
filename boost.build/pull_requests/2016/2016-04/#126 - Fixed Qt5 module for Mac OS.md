# #126 Fixed Qt5 module for Mac OS [Closed]

> Username: DmitryKuk  
> Created at: 2016-04-06 17:56:04 UTC  
> Updated at: 2021-10-02 21:18:55 UTC  
> Closed at: 2019-05-06 15:26:53 UTC  
> Url: https://github.com/boostorg/build/pull/126  

Hello!  
On Mac OS X (I have 10.10.5) Qt5 is distributed mostly as set of frameworks (qt-5.6 online installer and MacPorts checked).  
Each framework in Mac OS represented as directory and contains shared library (without prefix "lib" and any extension), headers and some other files. So linking commands are different, but compiler (clang) still knows how to link it.  
  
Compiler options for linking shared library may be like:  
`-L<path/to/libs> -lQtGui`  
But for framework they are:  
`-F<path/to/frameworks> -framework QtGui`  
  
I did some little changes to fix that, if `[ modules.peek : OS ] = MACOSX`.  
Without this fix you will get error messages (-lQtGui not found) on Mac OS.

---

## Comment 1

> Username: jhunold  
> Created_at: 2016-07-24 09:07:04 UTC  
> Updated_at: 2016-07-24 09:08:31 UTC  
> Url: https://github.com/boostorg/build/pull/126#issuecomment-234766293  

Using `if $(host-os) = MACOSX` and `modules.peek` is wrong and leads to lots of code duplication.  
Can you please check if simply adding   
  
`  
<target-os>darwin:<linkflags>"-F$(.prefix)/lib"  
<target-os>darwin:<linkflags>"-framework $(lib-name)"  
`  
  
in the `usage-requirements`-section will fix this, too?

---
