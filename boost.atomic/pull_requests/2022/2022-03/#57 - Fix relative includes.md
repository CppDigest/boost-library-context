# #57 Fix relative includes [Closed]

> Username: Flamefire  
> Created at: 2022-03-06 12:09:48 UTC  
> Updated at: 2024-12-09 10:26:37 UTC  
> Closed at: 2022-03-06 22:21:44 UTC  
> Url: https://github.com/boostorg/atomic/pull/57  

This adds a test job on Appveyor using a more recent Cygwin to reproduce #56: The use of relative includes requires explicitly adding the current source files path on some systems.  
This is then done for the few cases where relative includes are used:  
- src/find_address_sse2.cpp  
- src/find_address_sse41.cpp  
- various test files  
  
Note that the src folder is already added to the include-path however this doesn't affect the arch-specific files which have separate build rules and hence need that requirement explicitly there.  
  
I'm not fully sure about the distinction between the B2 `project` and `lib` rules but moving the `<include>../src` from the `lib` to the `project` requirements is also a solution to this issue and avoids the repetition of that at 3 places. But I'm not sure if that has any unwanted side effects. Especially as `<define>BOOST_ATOMIC_SOURCE` as well as the DYN_LINK/STATIC_LINK defines are also repeated between the `project` rule and the arch-specific `obj` rules for which I don't understand the reason.

---

## Comment 1

> Username: Lastique  
> Created_at: 2022-03-06 22:23:19 UTC  
> Url: https://github.com/boostorg/atomic/pull/57#issuecomment-1060050925  

Thanks, I've committed a slightly different change.  
  
I did not add the new Appveyor job as it is [failing](https://ci.appveyor.com/project/Lastique/atomic/builds/42803672/job/vh53899lqe5aj9mr) because of Boost.Thread. You may want to create a similar issue for Boost.Thread.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2022-03-07 09:38:21 UTC  
> Updated_at: 2022-03-07 09:38:51 UTC  
> Url: https://github.com/boostorg/atomic/pull/57#issuecomment-1060393939  

Ah, so the `project` approach works, see my last paragraph which I added recently.  
  
BTW: You may want to clean up the duplicated lines from the `obj` rule too:  
```  
            <link>shared:<define>BOOST_ATOMIC_DYN_LINK=1  
            <link>static:<define>BOOST_ATOMIC_STATIC_LINK=1  
            <define>BOOST_ATOMIC_SOURCE  
```  
Those should be inherited from the `project` too, maybe others as well, but those were the ones I noticed.  
  
The PR for Boost.Thread is https://github.com/boostorg/thread/pull/365

---
