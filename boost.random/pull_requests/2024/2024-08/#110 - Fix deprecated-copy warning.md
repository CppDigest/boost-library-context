# #110 Fix deprecated-copy warning [Closed]

> Username: jcstroud  
> Created at: 2024-08-16 11:14:53 UTC  
> Updated at: 2024-08-19 12:53:04 UTC  
> Closed at: 2024-08-16 22:40:28 UTC  
> Url: https://github.com/boostorg/random/pull/110  

# Fixed Deprecated Copy Constructor Warning in `polynomial.hpp`  
  
## Issue Description  
  
The `boost/random/detail/polynomial.hpp` triggers compiler warnings related to a deprecated implicit copy constructor:  
  
```  
warning: definition of implicit copy constructor for 'reference' is deprecated because it has a user-declared copy assignment operator [-Wdeprecated-copy]  
```  
  
This warning occurs in C++11 and later standards when a class has a user-declared copy assignment operator but no user-declared copy constructor.  
  
## Cause  
  
The `reference` class has a user-declared copy assignment operator:  
  
```cpp  
reference &operator=(const reference &other)  
```  
  
However, this reference class lacks an explicitly declared copy constructor.  
  
## Nature of the Fix  
  
The fix explicitly declares both a copy constructor and a move constructor for `reference`, adding the following lines:  
  
```cpp  
reference(const reference& other) = default;  
reference(reference&& other) = default;  
```  
  
When declaring these constructors as `default`, the compiler generates default implementations, thus:  
  
1. Silencing the deprecation warning for the implicit copy constructor.  
2. Maintaining the existing behavior of the `reference` class.  
3. Ensuring that the class can still be copied and moved as before, preserving its functionality.  
  
## Impact of the Fix  
  
This fix resolves the compiler warnings without changing the functionality of the `reference` class.  
  
The fix is backwards-compatible and should not introduce any behavioral changes in existing code.  
  
## Conclusion  
  
This update brings the `polynomial.hpp` file in line with current C++ standards and best practices. It eliminates deprecation warnings while maintaining existing functionality.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-08-16 12:22:14 UTC  
> Updated_at: 2024-08-16 22:40:35 UTC  
> Url: https://github.com/boostorg/random/pull/110#issuecomment-2293413028  

## [Codecov](https://app.codecov.io/gh/boostorg/random/pull/110?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.84%. Comparing base [(`560acd8`)](https://app.codecov.io/gh/boostorg/random/commit/560acd810445ca63b07730e47d2dae3f32a659ec?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`21ba664`)](https://app.codecov.io/gh/boostorg/random/commit/21ba664d07e179eb37ff3736e6b52012db272dd0?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/random/pull/110/graphs/tree.svg?width=650&height=150&src=pr&token=7wvW4uAjLp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/random/pull/110?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #110   +/-   ##  
========================================  
  Coverage    94.84%   94.84%             
========================================  
  Files          101      101             
  Lines         6242     6242             
========================================  
  Hits          5920     5920             
  Misses         322      322             
```  
  
| [Files](https://app.codecov.io/gh/boostorg/random/pull/110?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/random/detail/polynomial.hpp](https://app.codecov.io/gh/boostorg/random/pull/110?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fdetail%2Fpolynomial.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20vZGV0YWlsL3BvbHlub21pYWwuaHBw) | `96.93% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/random/pull/110?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/random/pull/110?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [560acd8...21ba664](https://app.codecov.io/gh/boostorg/random/pull/110?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: jcstroud  
> Created_at: 2024-08-16 22:40:28 UTC  
> Url: https://github.com/boostorg/random/pull/110#issuecomment-2294409255  

Many checks were not successful, so I'm going to close this pull request and make another with extended compiler backwards compatibility. I'll note the forwards-compatible fix in my next attempt.

---

## Comment 3

> Username: mborland  
> Created_at: 2024-08-19 12:53:02 UTC  
> Url: https://github.com/boostorg/random/pull/110#issuecomment-2296506797  

I believe this fix is correct. The issues that I see in the CI are coming from 1) Github breaking the Node version in Actions, and 2) The boost.math dependency makes it so this library needs to be built now from C++14 and not from C++11. I'll fix up the CI system.

---
