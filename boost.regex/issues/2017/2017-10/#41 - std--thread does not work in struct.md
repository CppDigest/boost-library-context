# #41 - std::thread does not work in struct [Closed]

> Username: jmichae3  
> Created at: 2017-10-14 17:59:31 UTC  
> Updated at: 2017-10-18 18:46:09 UTC  
> Closed at: 2017-10-18 18:46:09 UTC  
> Url: https://github.com/boostorg/regex/issues/41  

try putting std::thread in struct and later trying to fill in blanks. it can't be done.  
also, you have to CloseHandle() in windows when thread is done and people don't know this. which may explain any server resource leaks. that you can't do without a vector or array of struct.

---

## Comment 1

> Username: jmichae3  
> Created at: 2017-10-14 18:26:48 UTC  
> Url: https://github.com/boostorg/regex/issues/41#issuecomment-336654237  

- need to be able to instantiate an empty object at the start and fill in thread function and run it and maybe even close it later, even within a struct.  
- constructor should not require running and configuring the thread upon instantiation.  
- std::thread.nativeHandle() should allow someone to CloseHandle() on windows.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2017-10-15 08:38:39 UTC  
> Url: https://github.com/boostorg/regex/issues/41#issuecomment-336695403  

What has this to do with Boost.Regex?

---

## Comment 3

> Username: jmichae3  
> Created at: 2017-10-17 01:58:11 UTC  
> Url: https://github.com/boostorg/regex/issues/41#issuecomment-337094564  

sorry, wrong group
