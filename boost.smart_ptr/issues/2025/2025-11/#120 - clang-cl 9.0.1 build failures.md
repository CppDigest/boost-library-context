# #120 - clang-cl 9.0.1 build failures [Open]

> Username: Lastique  
> Created at: 2025-11-13 19:43:26 UTC  
> Updated at: 2025-11-14 19:26:56 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/120  

Boost.Iterator CI fails with the following errors:  
  
```  
compile-c-c++ bin.v2\libs\iterator\test\indirect_iterator_test.test\clang-win-9.0.1\debug\x86_32\cxxstd-latest-iso\threading-multi\indirect_iterator_test.obj  
In file included from libs\iterator\test\indirect_iterator_test.cpp:25:  
In file included from .\boost/shared_ptr.hpp:17:  
In file included from .\boost/smart_ptr/shared_ptr.hpp:28:  
.\boost/smart_ptr/detail/spinlock_pool.hpp(77,5): error: no matching constructor for initialization of 'std::atomic_flag'  
    BOOST_DETAIL_SPINLOCK_INIT, BOOST_DETAIL_SPINLOCK_INIT, BOOST_DETAIL_SPINLOCK_INIT, BOOST_DETAIL_SPINLOCK_INIT, BOOST_DETAIL_SPINLOCK_INIT,   
    ^~~~~~~~~~~~~~~~~~~~~~~~~~  
.\boost/smart_ptr/detail/spinlock_std_atomic.hpp(88,38): note: expanded from macro 'BOOST_DETAIL_SPINLOCK_INIT'  
#define BOOST_DETAIL_SPINLOCK_INIT { ATOMIC_FLAG_INIT }  
                                     ^~~~~~~~~~~~~~~~  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.16.27023\include\atomic(158,26): note: expanded from macro 'ATOMIC_FLAG_INIT'  
#define ATOMIC_FLAG_INIT        {0}  
                                ^~~  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.16.27023\include\atomic(169,2): note: candidate constructor not viable: no known conversion from 'int' to 'const std::atomic_flag' for 1st argument  
        atomic_flag(const atomic_flag&) = delete;  
        ^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.16.27023\include\atomic(168,2): note: candidate constructor not viable: requires 0 arguments, but 1 was provided  
        atomic_flag() noexcept = default;  
        ^  
```  
  
See [here](https://ci.appveyor.com/project/Lastique/iterator/builds/53068028/job/9a969r9a51bu82ge?fullLog=true#L2002) for the full log.  
  
I've also noticed similar failures in Boost.Log [here](https://github.com/boostorg/log/actions/runs/19117847394/job/54631341160) with clang 3.5 and 3.6, but those compilers were rather old. This one is not.

---

## Comment 1

> Username: pdimov  
> Created at: 2025-11-13 20:32:07 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/120#issuecomment-3529604877  

I'm seeing this Appveyor failure in a number of repos, but at the moment my inclination is to just drop the 2017 clang-cl jobs. This is another case of a configuration that probably no real person uses apart from our Appveyor CI. I struggle to come up with a reason for anyone to use clang-cl 9 today.  
  
People still on VS 2017 exist, but I doubt they'd use clang-cl 9.  
  
If I receive bug reports from actual users, I'll probably reconsider.

---

## Comment 2

> Username: pdimov  
> Created at: 2025-11-14 18:42:53 UTC  
> Updated at: 2025-11-14 19:26:56 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/120#issuecomment-3534095244  

I changed my mind. Old Clangs (3.5, 3.6, clang-cl 9) should work again.

---

## Comment 3

> Username: Lastique  
> Created at: 2025-11-14 19:07:22 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/120#issuecomment-3534176442  

Thanks. I suppose, this should be included in the upcoming release?

---

## Comment 4

> Username: pdimov  
> Created at: 2025-11-14 19:25:54 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/120#issuecomment-3534247877  

I'll wait for all the CI to cycle and ask.
