# #151 - Crashes on Windows [Closed]

> Username: qiaone  
> Created at: 2017-11-05 09:15:09 UTC  
> Updated at: 2017-11-05 15:11:11 UTC  
> Closed at: 2017-11-05 15:11:11 UTC  
> Url: https://github.com/boostorg/fiber/issues/151  

**Tested Platforms:**  
1, A Lenovo laptop with Win 8.1 VS2015 update 3  
2, A Macbook Pro laptop with Win 8.1 VS2017.2  
3, A Intel Core i5 16GB RAM desktop with Win7 VS2017.2  
4, A Intel Core i5 8GB RAM desktop with Win8.1 VS2015 update 3  
  
**Tested Versions:**  
1, Boost 1.65.1  
2, Boost 1.65.1 + Dev branch of Boost.Context and Boost.Fiber  
3. Boost 1.64.0  
  
**Build Config:**  
`b2 variant=debug threading=multi link=static runtime-link=static address-model=64 stage`  
  
**VS Config:**  
1, Debug build  
2, Static linking of the C++ runtime  
3, Stack reserved size increased to 4M/8M/16M but not helped  
  
**Code:**  
All the code samples from the  `performance/fiber` in the repo crashed.  
```  
fiber/performance/fiber/skynet_detach.cpp  
fiber/performance/fiber/skynet_join.cpp  
fiber/performance/fiber/skynet_shared_detach.cpp  
...  
```  
  
**Call stacks screenshots:**  
  
<img width="651" alt="snipaste_20171106_005103" src="https://user-images.githubusercontent.com/5222364/32413485-089f645a-c24d-11e7-92b8-c36370241274.png">  
  
<img width="734" alt="snipaste_20171106_005304" src="https://user-images.githubusercontent.com/5222364/32413489-16d14782-c24d-11e7-83ff-6070efdb47eb.png">  
  
<img width="751" alt="snipaste_20171106_005352" src="https://user-images.githubusercontent.com/5222364/32413491-1c246d86-c24d-11e7-82da-830a4f10b482.png">  
  
<img width="659" alt="snipaste_20171106_005531" src="https://user-images.githubusercontent.com/5222364/32413493-21b0fbe8-c24d-11e7-986a-f08973275d72.png">

---

## Comment 1

> Username: olk  
> Created at: 2017-11-05 09:25:40 UTC  
> Url: https://github.com/boostorg/fiber/issues/151#issuecomment-341959836  

"The maximum number of stack frames supported by Visual Studio hasbeen exceeded." ?

---

## Comment 2

> Username: qiaone  
> Created at: 2017-11-05 09:35:13 UTC  
> Url: https://github.com/boostorg/fiber/issues/151#issuecomment-341960340  

Yeah, I think it's a limitation of the VS debugger which lead to incomplete display of the callstacks. But the code in work_stealing::pick_next() do crashes a lot with various callstacks.

---

## Comment 3

> Username: olk  
> Created at: 2017-11-05 10:22:53 UTC  
> Url: https://github.com/boostorg/fiber/issues/151#issuecomment-341962612  

I don't use Windows, my development platform is Linux and its does not crash even if I execute skynet_stealing_detach in a loop for hours.  
You could try to enable the WinFiber support : compile boost.context + boost.fiber with context-impl=winfib.

---

## Comment 4

> Username: olk  
> Created at: 2017-11-05 14:00:51 UTC  
> Url: https://github.com/boostorg/fiber/issues/151#issuecomment-341975233  

Maybe VC adds TLS and if a fiber is migrated to another thread you will get a crash.

---

## Comment 5

> Username: qiaone  
> Created at: 2017-11-05 14:47:09 UTC  
> Url: https://github.com/boostorg/fiber/issues/151#issuecomment-341978461  

I finally got all code samples working using WinFiber as you suggested. I'll try to look into the problem and hopefully find some clues why it crashes when using fcontext. Thanks for all the help.

---

## Comment 6

> Username: olk  
> Created at: 2017-11-05 15:11:09 UTC  
> Url: https://github.com/boostorg/fiber/issues/151#issuecomment-341980196  

Another reason could be the TIB (thread information block) which is not fully described in MSDN. Maybe some special parts are not swapped in the assembler impl of boost.context (Gor Nishanov has told me  something ...).
