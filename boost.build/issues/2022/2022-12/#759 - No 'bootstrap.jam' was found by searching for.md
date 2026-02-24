# #759 - No 'bootstrap.jam' was found by searching for: [Closed]

> Username: denissanga  
> Created at: 2022-12-30 08:18:25 UTC  
> Updated at: 2022-12-30 08:18:39 UTC  
> Closed at: 2022-12-30 08:18:39 UTC  
> Url: https://github.com/boostorg/build/issues/759  

Hi all, I'm trying compiling bootstrap on windows but I obtain follogin message  
```  
C:\Programmi\boost_1_81_0\tools\build>b2 --build-dir=build-directory toolset=toolset-name --build-type=complete stage  
Unable to load B2  
-----------------  
No 'bootstrap.jam' was found by searching for:  
  C:/Programmi/boost_1_81_0/tools/build/.b2/kernel/bootstrap.jam  
  C:/Programmi/boost_1_81_0/tools/share/b2/kernel/bootstrap.jam  
  C:/Programmi/boost_1_81_0/tools/share/b2/src/kernel/bootstrap.jam  
  C:/Programmi/boost_1_81_0/tools/kernel/bootstrap.jam  
  C:/Programmi/boost_1_81_0/tools/build/src/kernel/bootstrap.jam  
  C:/Programmi/boost_1_81_0/tools/build/tools/build/src/kernel/bootstrap.jam  
  C:/Programmi/boost_1_81_0/tools/build/src/kernel/bootstrap.jam  
  C:/Programmi/boost_1_81_0/tools/src/kernel/bootstrap.jam  
  C:/Programmi/boost_1_81_0/src/kernel/bootstrap.jam  
  C:/Programmi/src/kernel/bootstrap.jam  
  C:/src/kernel/bootstrap.jam  
  
Please consult the documentation at 'https://www.bfgroup.xyz/b2/'.  
```  
  
can someone help me?  
many thanks in advance

---

## Comment 1

> Username: github-actions[bot]  
> Created at: 2022-12-30 08:18:37 UTC  
> Url: https://github.com/boostorg/build/issues/759#issuecomment-1367787590  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues
