# #100 - fibonacci example throws forced_unwind on mingw [Closed]

> Username: jschueller  
> Created at: 2019-03-08 16:08:41 UTC  
> Updated at: 2020-04-24 09:05:09 UTC  
> Closed at: 2020-04-24 09:05:09 UTC  
> Url: https://github.com/boostorg/context/issues/100  

on mingw (crosscompiling from Fedora 29, gcc 8.3.0, both x86_64 and i686 targets) the fibonacci example throws  
```  
0 1 1 2 3 5 8 13 21 34   
main: done  
terminate called after throwing an instance of 'boost::context::detail::forced_unwind'  
```  
  
To reproduce:  
```  
FROM fedora:29  
RUN dnf install -y mingw64-boost wine-devel  
WORKDIR /tmp  
RUN curl -fsSLO https://raw.githubusercontent.com/boostorg/context/develop/example/execution_context_v2/fibonacci.cpp  
RUN x86_64-w64-mingw32-g++ fibonacci.cpp -lboost_context-mt  
RUN WINEPATH=/usr/x86_64-w64-mingw32/sys-root/mingw/bin/ WINEARCH=win64 wine ./a.exe  
```  
  
Here Fedora uses boost 1.66 but I checked the same problem occurs with 1.69 on another box

---

## Comment 1

> Username: olk  
> Created at: 2020-04-24 09:05:09 UTC  
> Url: https://github.com/boostorg/context/issues/100#issuecomment-618895541  

outdated
