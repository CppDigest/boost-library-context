# #184 - can't put std::thread in vector of struct with other thread-related stuff, or class [Closed]

> Username: jmichae3  
> Created at: 2017-10-17 01:59:49 UTC  
> Updated at: 2017-10-17 05:40:13 UTC  
> Closed at: 2017-10-17 05:38:38 UTC  
> Url: https://github.com/boostorg/thread/issues/184  

try putting std::thread in struct and later trying to fill in blanks. it can't be done.  
also, you have to CloseHandle() in windows when thread is done and people don't know this. which may explain any server resource leaks. that you can't do without a vector or array of struct.  
  
- need to be able to instantiate an empty object at the start and fill in thread function and run it and  
- maybe even close it later, even within a struct.  
- constructor should not require running and configuring the thread upon instantiation.  
- std::thread.nativeHandle() should allow someone to CloseHandle() on windows.

---

## Comment 1

> Username: viboes  
> Created at: 2017-10-17 05:38:38 UTC  
> Url: https://github.com/boostorg/thread/issues/184#issuecomment-337123260  

Why are you creating those issues in this repo?
