# #568 Select appropriate QNX target platform [Merged]

> Username: karzhenkov  
> Created at: 2020-04-16 06:24:17 UTC  
> Updated at: 2021-10-02 21:02:20 UTC  
> Merged at: 2020-04-16 15:12:33 UTC  
> Closed at: 2020-04-16 15:12:34 UTC  
> Url: https://github.com/boostorg/build/pull/568  

The `qcc` compliler supports a couple of target platforms and has an option "-V" to select one of them. If this option is misisng, the compiler uses the default from its local configuration.  
  
This option isn't set by `qcc.jam`, so `<acrhitecture>` and `<address-model>` in the user build request are effectively ignored.  
  
To use non-default target platform, user has to  
* make it default (through compiler's local settings), or  
* manually add appropriate "-V" option to `<cxxflags>`, `<linkflags>` and `<asmflags>`.  
  
Obviously, these workarounds are error-prone and inconsistent. A better approach is to deduce desired option value from `<architecture>` and `<address-model>`.

---

## Review 1 [Changes requested]

> Username: grafikrobot  
> Created_at: 2020-04-16 12:13:43 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/568#pullrequestreview-394570688  

Looks great! Could you add a copyright for yourself to the file though?

---

## Comment 2

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:02:19 UTC  
> Url: https://github.com/boostorg/build/pull/568#issuecomment-932820061  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
