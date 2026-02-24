# #49 - doc sample code cannot compile [Closed]

> Username: j3soon  
> Created at: 2018-09-10 06:40:47 UTC  
> Updated at: 2018-09-12 06:22:26 UTC  
> Closed at: 2018-09-12 06:22:26 UTC  
> Url: https://github.com/boostorg/process/issues/49  

In [https://www.boost.org/doc/libs/1_68_0/doc/html/boost_process/tutorial.html#boost_process.tutorial.error_handling](https://www.boost.org/doc/libs/1_68_0/doc/html/boost_process/tutorial.html#boost_process.tutorial.error_handling)'s `Error` section:  
The original code:  
```cpp  
std::error_code ec;  
bp::system c("g++ main.cpp", ec);  
```  
I think it should be:  
```cpp  
std::error_code ec;  
bp::system("g++ main.cpp", ec);  
```

---

## Comment 1

> Username: j3soon  
> Created at: 2018-09-10 06:41:15 UTC  
> Url: https://github.com/boostorg/process/issues/49#issuecomment-419805001  

btw, in [https://www.boost.org/doc/libs/1_65_1/doc/html/boost_process/tutorial.html#boost_process.tutorial.group](https://www.boost.org/doc/libs/1_65_1/doc/html/boost_process/tutorial.html#boost_process.tutorial.group)'s `Group` section:  
There's a typo:  
```  
1. Being able two terminate child processes of the child process   
```  
that should be:  
```  
1. Being able to terminate child processes of the child process   
```

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2018-09-11 06:37:47 UTC  
> Url: https://github.com/boostorg/process/issues/49#issuecomment-420163812  

Thanks!

---

## Comment 3

> Username: j3soon  
> Created at: 2018-09-11 11:01:49 UTC  
> Url: https://github.com/boostorg/process/issues/49#issuecomment-420234720  

Thank you, how about the `Group` section? I think `to` is mistyped to `two`.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2018-09-12 06:22:24 UTC  
> Url: https://github.com/boostorg/process/issues/49#issuecomment-420526336  

Seems to look fine already https://github.com/boostorg/process/blob/develop/doc/tutorial.qbk#L343
