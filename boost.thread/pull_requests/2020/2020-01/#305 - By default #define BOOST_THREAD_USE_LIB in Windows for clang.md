# #305 By default #define BOOST_THREAD_USE_LIB in Windows for clang [Closed]

> Username: dmenendez-gruposantander  
> Created at: 2020-01-22 10:54:59 UTC  
> Updated at: 2020-01-27 10:29:06 UTC  
> Closed at: 2020-01-27 10:27:45 UTC  
> Url: https://github.com/boostorg/thread/pull/305  

I was trying to compile my code in Windows with clang-cl and BOOST_ALL_NO_LIB and I was getting weird linker errors about missing *imported* symbols:  undefined symbol: __declspec(dllimport) public: void __thiscall boost::thread::join(void)  
  
This led me to find out that clang-cl was a second class citizen wrt the definition of BOOST_THREAD_USE_LIB by default in Windows.  
  
I propose to add clang to the compilers that get BOOST_THREAD_USE_LIB defined by default in Windows.

---

## Comment 1

> Username: dmenendez-gruposantander  
> Created_at: 2020-01-27 10:29:06 UTC  
> Url: https://github.com/boostorg/thread/pull/305#issuecomment-578684854  

I see PR #290 already took care of this, but I was confused because I didn't see it merged in the 1.72.0 release

---
