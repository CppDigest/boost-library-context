# #27 - travis error [Closed]

> Username: sdarwin  
> Created at: 2021-02-18 00:46:02 UTC  
> Updated at: 2021-02-18 19:46:03 UTC  
> Closed at: 2021-02-18 08:41:06 UTC  
> Url: https://github.com/boostorg/integer/issues/27  

One travis job is failing, I also get the same error in tests.   
Any ideas? https://travis-ci.org/github/boostorg/integer/jobs/756933421  
  
```  
...failed updating 3 targets...  
...skipped 9 targets...  
...updated 333 targets...  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2021-02-18 08:41:06 UTC  
> Url: https://github.com/boostorg/integer/issues/27#issuecomment-781179501  

The error is [caused](https://travis-ci.org/github/boostorg/integer/jobs/756933421#L810) by Boost.Multiprecision, please report it [there](https://github.com/boostorg/multiprecision/issues/new/choose).

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-02-18 16:47:43 UTC  
> Url: https://github.com/boostorg/integer/issues/27#issuecomment-781481307  

Hmmm... I suspect gcc-4.8 is going to be on the unsupported list for Multiprecision.  I realise it has pre-C++11 support but there are enough differences to make support not worthwhile at this point.  Though if there are enough complaints I might reconsider.  
  
If you wanted to work around this here, then adding cxx11_type_traits to the requires clause for those tests would do the trick.  I think the thing to do is to add an alias target to multiprecision with the "official" usage requirements and then propagate it's use here.  I'll file a PR once the necessary target is in Multiprecision and the master project has cycled and updated.

---

## Comment 3

> Username: NAThompson  
> Created at: 2021-02-18 17:21:10 UTC  
> Url: https://github.com/boostorg/integer/issues/27#issuecomment-781504489  

Here's my vote for deprecating gcc 4.8.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2021-02-18 18:43:08 UTC  
> Updated at: 2021-02-18 18:43:28 UTC  
> Url: https://github.com/boostorg/integer/issues/27#issuecomment-781556387  

Apparently alias targets don't propagate `<build>no`, so I've filed the usual manual fix.

---

## Comment 5

> Username: Lastique  
> Created at: 2021-02-18 19:42:48 UTC  
> Url: https://github.com/boostorg/integer/issues/27#issuecomment-781589793  

@jzmaddock @NAThompson While you could deprecate gcc 4.8, the build log indicates genuine bugs, like mismatching `const` vs `constexpr`.  
  
Also, Boost.Multiprecision use in Boost.Integer tests is conditioned on C++ compliance checks [here](https://github.com/boostorg/integer/blob/092d94c833a956054a240dd182001aaad2aac75e/test/multiprecision_config.hpp#L21), which were taken from Boost.Multiprecision sources. The fact that the tests with Boost.Multiprecision run on gcc 4.8 means the compiler is compliant. If you're planning to deprecate it then please specify which C++ feature it is missing so I can add it to the list of checks.

---

## Comment 6

> Username: Lastique  
> Created at: 2021-02-18 19:46:03 UTC  
> Url: https://github.com/boostorg/integer/issues/27#issuecomment-781592402  

> then please specify which C++ feature it is missing so I can add it to the list of checks.  
  
Oh, I didn't see your PR, sorry.
