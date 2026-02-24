# #53 - Build instructions [Closed]

> Username: vinniefalco  
> Created at: 2016-08-17 14:55:38 UTC  
> Updated at: 2016-08-26 17:44:06 UTC  
> Closed at: 2016-08-26 17:44:06 UTC  
> Url: https://github.com/boostorg/beast/issues/53  

The README.md needs a section with complete instructions for building and running the tests and examples. Especially how to install Boost with bjam in the path.

---

## Comment 1

> Username: 37  
> Created at: 2016-08-23 06:06:34 UTC  
> Url: https://github.com/boostorg/beast/issues/53#issuecomment-241635782  

I'm having a bit of an issue issue with exactly this, any tips on how to include the header files with CMakeList?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-08-23 10:08:12 UTC  
> Url: https://github.com/boostorg/beast/issues/53#issuecomment-241686353  

I'm not sure I understand. Are you trying to build the tests and examples? From the root directory of Beast, do this:  
  
```  
mkdir bin  
cd bin  
cmake ..  
```  
  
The bin/ directory will now contain a Makefile, or a Visual Studio project file depending on your configured build tools.  
  
Or are you asking me how to set up _your_ CMakeLists.txt in order to use Beast? In that case, just add a line to your CMakeLIsts.txt to add the `include/` directory to be searched by the compiler. Your include lines for Beast would look like this:  
  
```  
#include <beast/websocket.hpp>  
```

---

## Comment 3

> Username: 37  
> Created at: 2016-08-24 06:37:10 UTC  
> Updated at: 2016-08-24 06:38:19 UTC  
> Url: https://github.com/boostorg/beast/issues/53#issuecomment-241970975  

Thanks for that @vinniefalco. When trying building cmake wasn't playing nicely so I just assumed I had to build it another way (having seen the scripts) or just include it as a local library somehow.   
  
In the end I figured it out, and have some requirements to add:  
- cmake > v3.20  
- libpthread   
- boost > v1.58 (needs to be version 1.58 or later to make use of the endian library, ubuntu 14.04 repos only have up to 1.55 - so building from source is necessary).   
  
I'm happy to go ahead and update the README to include the above and be more novice friendly if you would like?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2016-08-24 11:36:11 UTC  
> Url: https://github.com/boostorg/beast/issues/53#issuecomment-242033531  

Sure, I would love an update to the README.md!  
  
One thing though, keep in mind that Beast is header-only so there's nothing to build except your own project. The only things that you build for Beast are the examples and the unit tests, but those are not required to use the library. Or are you talking about building your own executable that uses Beast?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2016-08-26 17:44:06 UTC  
> Url: https://github.com/boostorg/beast/issues/53#issuecomment-242802589  

Merged, thanks for the contribution!
