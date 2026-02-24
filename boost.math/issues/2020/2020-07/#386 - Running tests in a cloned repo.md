# #386 - Running tests in a cloned repo [Closed]

> Username: evanmiller  
> Created at: 2020-07-02 01:51:11 UTC  
> Updated at: 2020-07-03 11:07:30 UTC  
> Closed at: 2020-07-03 11:07:29 UTC  
> Url: https://github.com/boostorg/math/issues/386  

I am trying to juggle a couple of different checkouts of the math library to test regressions etc. I was hoping I could:  
  
1. Check out boost and submodules  
2. Move `math` to `math.bak`  
3. Check out my cloned repo to `math`  
4. `cd` into `math/test`  
5. Run `../../../b2 some_test`  
  
However when I do that I get:  
  
```  
../../../b2 test_jacobi  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes  
error: Cannot create link ../../../boost/math_fwd.hpp to ../include/boost/math_fwd.hpp.  
error: Link previously defined to another file, ../../math.bak/include/boost/math_fwd.hpp.  
```  
  
What am I doing wrong here? Is there a "clean" function to fix up the links? Have I made a grave error trying to move submodules around? How do others go about running tests in their cloned `math` repos?

---

## Comment 1

> Username: pabristow  
> Created at: 2020-07-02 07:34:22 UTC  
> Url: https://github.com/boostorg/math/issues/386#issuecomment-652838825  

Does adding  >b2 --reconfigure -a  ... help? and/or >b2 headers to force the symlinks to be reset?

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-07-02 12:20:39 UTC  
> Url: https://github.com/boostorg/math/issues/386#issuecomment-652972842  

Personally, I don't bother with making a `math.bak`; we've all got copies of the repo. My workflow:  
  
```  
$ git clone --recursive https://github.com/boostorg/boost.git  
boost/libs$ rm -rf math  
boost/libs$ git clone https://github.com/myfork/math.git  
```

---

## Comment 3

> Username: evanmiller  
> Created at: 2020-07-02 14:21:17 UTC  
> Url: https://github.com/boostorg/math/issues/386#issuecomment-653034935  

@pabristow Same error with both commands  
  
```  
./b2 --reconfigure -a  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : yes  
error: Cannot create link boost/math_fwd.hpp to libs/math/include/boost/math_fwd.hpp.  
error: Link previously defined to another file, libs/math.bak/include/boost/math_fwd.hpp.  
```  
  
```  
./b2 headers  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes  
error: Cannot create link boost/math_fwd.hpp to libs/math/include/boost/math_fwd.hpp.  
error: Link previously defined to another file, libs/math.bak/include/boost/math_fwd.hpp.  
```  
  
@NAThompson I have some of my changes scattered about different repos... was hoping I could swap them back and forth without needing completely separate copies of boost.

---

## Comment 4

> Username: pabristow  
> Created at: 2020-07-02 14:57:52 UTC  
> Url: https://github.com/boostorg/math/issues/386#issuecomment-653056592  

symlinks no doubt seemed like a good idea at time but I have found them troublesome (and confusing) if I stray off the well-beaten path.  
  
Do you get a complete list of all the links after doing  
  
>b2 -a headers  
  
You can also always safely delete any of the symlinks and indeed the whole /boost folder of symlinks.  Running b2 headers will always regenerate the symlinks.  
  
I have also resorted to manually deleting a symlink and creating another link.  
  
But in the end, Nick's suggestion may be better.

---

## Comment 5

> Username: evanmiller  
> Created at: 2020-07-02 19:10:22 UTC  
> Url: https://github.com/boostorg/math/issues/386#issuecomment-653176480  

```  
$ ./b2 -a headers  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes  
error: Cannot create link boost/math_fwd.hpp to libs/math/include/boost/math_fwd.hpp.  
error: Link previously defined to another file, libs/math.bak/include/boost/math_fwd.hpp.  
```  
  
@pabristow For whatever reason it appears that b2 is creating hard links rather than symlinks on my machine (macOS 10.15).  
  
```  
$ find . -type l  
./bin.v2/test-symlink  
$  
```

---

## Comment 6

> Username: pabristow  
> Created at: 2020-07-03 08:33:22 UTC  
> Url: https://github.com/boostorg/math/issues/386#issuecomment-653425097  

Not sure if that makes any difference - I suspect not, so still try my suggestions?  
  
I'd start with just deleting the obsolete link.

---

## Comment 7

> Username: evanmiller  
> Created at: 2020-07-03 11:07:29 UTC  
> Url: https://github.com/boostorg/math/issues/386#issuecomment-653492443  

@pabristow When I first tried deleting the link, it led me to another error message about an obsolete link and another etc. Because `b2` was creating hard links on my system, I had no way of telling that the whole folder was links. However, after updating boost, it is creating symlinks now.  
  
I would suggest adding an ability to `b2` to pave over links that are in conflict, and updating the error message to be more actionable to novice users.  
  
Anyway I'm following @NAThompson's workflow now and will close this issue.
