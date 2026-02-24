# #138 - fibonacci example throws forced_unwind on mingw [Closed]

> Username: jschueller  
> Created at: 2020-04-24 09:51:13 UTC  
> Updated at: 2022-09-18 14:10:05 UTC  
> Closed at: 2022-09-18 14:10:05 UTC  
> Url: https://github.com/boostorg/context/issues/138  

on mingw (crosscompiling from archlinux, gcc 9.3.0, x86_64) the fibonacci example throws  
  
```  
0 1 1 2 3 5 8 13 21 34   
main: done  
terminate called after throwing an instance of 'boost::context::detail::forced_unwind'  
```  
  
here are my exact commands:  
```  
cd /tmp  
curl -fsSLO  https://raw.githubusercontent.com/boostorg/context/develop/example/callcc/fibonacci.cpp  
x86_64-w64-mingw32-g++ fibonacci.cpp -lboost_context && x86_64-w64-mingw32-wine ./a.exe  
```  
I tried 1.73.0rc1

---

## Comment 1

> Username: olk  
> Created at: 2020-04-24 10:11:50 UTC  
> Url: https://github.com/boostorg/context/issues/138#issuecomment-618925380  

cross compiling is not supported - at least it had provided a lot or probs in the past

---

## Comment 2

> Username: jschueller  
> Created at: 2020-04-24 11:28:35 UTC  
> Url: https://github.com/boostorg/context/issues/138#issuecomment-618956266  

boost works quite nicely for something unsupported : )
