# #402 - Compilation error in future.hpp using LLVM upstream clang [Closed]

> Username: thevinster  
> Created at: 2024-05-29 08:52:17 UTC  
> Updated at: 2025-03-12 02:05:28 UTC  
> Closed at: 2025-03-12 02:05:28 UTC  
> Url: https://github.com/boostorg/thread/issues/402  

After this [patch](https://github.com/llvm/llvm-project/commit/8009bbec59d1c5d47ae06c431647ebee6d886ff2) in upstream LLVM, this line in `future.hpp`  
https://github.com/boostorg/thread/blob/aec18d337f41d8e3081ee65f5cf3b5090179ab0e/include/boost/thread/future.hpp#L4671  
fails to compile with the following error  
  
```  
boost/boost/thread/future.hpp:4672:19: error: no member named 'that' in 'run_it<FutureExecutorContinuationSharedState>'; did you mean 'that_'?  
 4672 |           that_=x.that;  
      |                   ^  
boost/boost/thread/future.hpp:4650:55: note: 'that_' declared here  
 4650 |     shared_ptr<FutureExecutorContinuationSharedState> that_;  
      |                                                       ^  
```   
  
From looking at the error and the code surrounding the error, is this a bug in `future.hpp` where the code should be using `x.that_` given other nearby uses of `x.that_`?

---

## Comment 1

> Username: DimitryAndric  
> Created at: 2024-08-09 14:04:59 UTC  
> Url: https://github.com/boostorg/thread/issues/402#issuecomment-2278026679  

Yes, in my opinion it is a typo in boost. The line should read:  
  
```c++  
          that_=x.that_;  
```

---

## Comment 2

> Username: arvid-norlander  
> Created at: 2024-11-01 12:47:14 UTC  
> Url: https://github.com/boostorg/thread/issues/402#issuecomment-2451817975  

I'm having trouble figuring out which release of boost the fix is included with. Based on release date and the date of the commit that fixes this I would have expected that 1.86 has the fix, but there is nothing in the [release notes](https://www.boost.org/users/history/version_1_86_0.html) about the thread library at all. So I guess it missed the deadline? And the code is still broken in 1.86 tar ball unfortunately.  
  
Will there be a patch release with the fix?

---

## Comment 3

> Username: DimitryAndric  
> Created at: 2024-11-01 13:00:13 UTC  
> Url: https://github.com/boostorg/thread/issues/402#issuecomment-2451834193  

As far as I can see, @zlojvavan only put this in a fork: https://github.com/zlojvavan/thread/commit/3f915121c4f828921e225252605cbe3e756dde94, so it won't be in any release tarball yet, I guess.

---

## Comment 4

> Username: arvid-norlander  
> Created at: 2024-11-01 13:08:49 UTC  
> Url: https://github.com/boostorg/thread/issues/402#issuecomment-2451845521  

Ah I missed that detail.

---

## Comment 5

> Username: arvid-norlander  
> Created at: 2024-11-01 13:11:34 UTC  
> Url: https://github.com/boostorg/thread/issues/402#issuecomment-2451849159  

Currently this breaks compilation with Clang 19, which has been released as of 2024-09-17. I believe it would make sense for boost to make a hotfix release of some sort to address this. Clang is after all one of the three big C++ implementations.
