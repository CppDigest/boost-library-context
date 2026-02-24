# #549 - 06_b_transformation_example.cpp shows compilation error!! [Closed]

> Username: divyanshu132  
> Created at: 2019-01-24 10:20:07 UTC  
> Updated at: 2019-01-28 13:34:18 UTC  
> Closed at: 2019-01-28 13:34:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/549  

![cpp](https://user-images.githubusercontent.com/36084189/51671748-b1ac1380-1fef-11e9-9ad2-c018a1df02eb.png)

---

## Comment 1

> Username: subhashreddykallam  
> Created at: 2019-01-28 13:14:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/549#issuecomment-458126605  

Hi @divyanshu132,  
I tried to reproduce the same error but the file successfully compiled and ran. Can you please check if you have linked the header files to the g++ compiler.  
  
![deepinscreenshot_20190128184143](https://user-images.githubusercontent.com/42376739/51838524-86e1f800-232c-11e9-8ff2-961cf712cb71.png)

---

## Comment 2

> Username: mloskot  
> Created at: 2019-01-28 13:34:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/549#issuecomment-458133764  

@divyanshu132 you are not pointing `g++` with `-I` where to find Boost headers.  
  
Here, GCC 8.2 compiles the example using Bosot 1.69 and runs it without any errors: https://wandbox.org/permlink/fc3t9b3rN6cMizmh   
  
It uses the `-I` in the command line it uses:  
  
```  
$ g++ prog.cc -Wall -Wextra -I/opt/wandbox/boost-1.69.0/gcc-8.2.0/include -std=c++11  
```  
  
You need to do similar, with path specific to your installation/environment.
