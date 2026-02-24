# #273 - Documentation: Use of chrono under MSVC can cause a large memory leak [Closed]

> Username: SpareSimian  
> Created at: 2024-05-24 05:35:22 UTC  
> Updated at: 2024-05-27 10:01:37 UTC  
> Closed at: 2024-05-27 10:01:37 UTC  
> Url: https://github.com/boostorg/mysql/issues/273  

FYI Using MySQL datetime support with STL <chrono> and C++20 can result in a memory leak being reported under MSVC. The timezone database (tzdb) is initialized in a way that's not protected from the debugger's leak detector.  
  
A workaround is offered here: https://github.com/microsoft/STL/issues/2504  
  
More on the problem here: https://github.com/microsoft/STL/issues/2047  
  
I put the workaround in my wxApp::OnExit handler and the leak no longer floods the debug output.  
  
```  
#ifdef _DEBUG  
   // to prevent the tzdb allocations from being reported as memory leaks  
   std::chrono::get_tzdb_list().~tzdb_list();  
#endif  
```

---

## Comment 1

> Username: anarthal  
> Created at: 2024-05-24 10:48:52 UTC  
> Url: https://github.com/boostorg/mysql/issues/273#issuecomment-2129229494  

So if I understood correctly, this is a problem in MSVC STL that won't get fixed at all, isn't it? What can we do in MySQL about it? Maybe add the info you provided to the documentation to guide other users encountering it?

---

## Comment 2

> Username: anarthal  
> Created at: 2024-05-24 10:49:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/273#issuecomment-2129229947  

I just saw the issue title :)
