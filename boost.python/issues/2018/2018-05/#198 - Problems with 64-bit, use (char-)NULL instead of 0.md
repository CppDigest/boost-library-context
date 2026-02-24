# #198 - Problems with 64-bit, use (char*)NULL instead of 0 [Closed]

> Username: TheCrazyT  
> Created at: 2018-05-03 18:50:38 UTC  
> Updated at: 2018-05-06 21:36:04 UTC  
> Closed at: 2018-05-06 21:36:03 UTC  
> Url: https://github.com/boostorg/python/issues/198  

Title says it all, the following lines can cause problems:  
  
https://github.com/boostorg/python/blob/28e2c6512cf3878518dff82532650144e849198e/src/object/class.cpp#L621  
  
https://github.com/boostorg/python/blob/28e2c6512cf3878518dff82532650144e849198e/src/object/class.cpp#L631  
  
Its better to use (char*)NULL instead.  
I was getting unexpected behavior while execution of an application when i compiled the library with mingw.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-05-03 19:02:30 UTC  
> Url: https://github.com/boostorg/python/issues/198#issuecomment-386402690  

Please give some context. What compiler are you using ? And what error are you observing ? Can you produce a minimal self-contained test case for me to reproduce the error ?

---

## Comment 2

> Username: TheCrazyT  
> Created at: 2018-05-04 15:59:09 UTC  
> Updated at: 2018-05-04 15:59:51 UTC  
> Url: https://github.com/boostorg/python/issues/198#issuecomment-386646317  

Oh well i have made a small example at stackoverflow:   
https://stackoverflow.com/questions/50140750/possible-bug-with-variadic-arguments-on-mingw64-gcc  
Basically i first thought its a compiler bug, but it is indeed a bug at the mentioned locations.  
The real project that had problems can be found here:  
https://github.com/TheCrazyT/roboschool  
It used a cross compiled library of boost_python that can be found here:  
https://github.com/TheCrazyT/boost_python_build_win  
(boost library gets crossbuild by travis-ci.org with x86_64-w64-mingw32-g++ and uploaded to bintray)  
Without the patch file (https://github.com/TheCrazyT/boost_python_build_win/blob/master/add_property.patch) the "roboschool"-project for windows was not executable.

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2018-05-06 21:36:03 UTC  
> Url: https://github.com/boostorg/python/issues/198#issuecomment-386917185  

OK, thanks. This is fixed as per https://github.com/boostorg/python/commit/467a89eba794799e5e62a3945f78fd1e0eec506c.
