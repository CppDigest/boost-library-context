# #148 - Can't link boost::interprocess library in Ubuntu. Lib non-existent? [Closed]

> Username: piffy  
> Created at: 2021-07-15 12:30:54 UTC  
> Updated at: 2021-07-22 16:30:52 UTC  
> Closed at: 2021-07-22 16:30:51 UTC  
> Url: https://github.com/boostorg/interprocess/issues/148  

I am moving my first steps with Boost. I am working on  the example program that uses boost::interprocess::shared memory , as shown in https://www.boost.org/doc/libs/1_49_0/doc/html/interprocess/sharedmemorybetweenprocesses.html  
  
Though the program compiles OK, I'm unable to link it.  I tried several CLI commands, the most promising being   
```  
g++ boostshm.cpp -lboost_interprocess -ltr  
```  
It states that it can't find lboost_interprocess. This is also confirmed by listing boost libraries in /usr/lib.  
  
Note: I am using Ubuntu 20.04.  I installed Boost with `sudo apt install libboost-all-dev`, and other Boost programs compile and link just fine.

---

## Comment 1

> Username: niXman  
> Created at: 2021-07-21 23:59:43 UTC  
> Url: https://github.com/boostorg/interprocess/issues/148#issuecomment-884571898  

as far as I know this is a header-only library.

---

## Comment 2

> Username: piffy  
> Created at: 2021-07-22 16:30:51 UTC  
> Url: https://github.com/boostorg/interprocess/issues/148#issuecomment-885049917  

You are right. I managed by linking the appropriate libraries.
