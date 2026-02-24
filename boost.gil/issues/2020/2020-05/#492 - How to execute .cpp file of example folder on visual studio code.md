# #492 - How to execute .cpp file of example folder on visual studio code. [Closed]

> Username: fettle-in  
> Created at: 2020-05-08 05:33:58 UTC  
> Updated at: 2020-05-08 08:11:44 UTC  
> Closed at: 2020-05-08 08:11:34 UTC  
> Url: https://github.com/boostorg/gil/issues/492  

while compiling ```.cpp``` file of `example` folder is showing error although I have updated my ```includePath``` in vsCode.   
this is the screenshot of errors .   
1. ![1](https://user-images.githubusercontent.com/60140601/81373594-b4fedc00-911a-11ea-927d-241ba952983c.png)  
2. ![2](https://user-images.githubusercontent.com/60140601/81373692-f8594a80-911a-11ea-87f7-6300b9c65cd8.png)  
last image is screenshot of my inlcudePath.  
  
will it be resolved by adding ```add_executable``` command in ```CmakeList.txt``` ???

---

## Comment 1

> Username: mloskot  
> Created at: 2020-05-08 08:11:34 UTC  
> Url: https://github.com/boostorg/gil/issues/492#issuecomment-625697820  

Sorry, but this is not the right place to look for basic help on how to use VSCode or CMake or GCC (If you look at the `g++ threshold.cpp`, the command does not tell the compiler where to look for Boost.
