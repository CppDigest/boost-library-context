# #94 Fix UB due to dangling references in try/catch [Merged]

> Username: mgaunard  
> Created at: 2020-03-22 16:59:27 UTC  
> Updated at: 2020-04-08 09:33:17 UTC  
> Merged at: 2020-04-08 09:33:17 UTC  
> Closed at: 2020-04-08 09:33:17 UTC  
> Url: https://github.com/boostorg/phoenix/pull/94  



---

## Comment 1

> Username: djowel  
> Created_at: 2020-03-22 21:01:31 UTC  
> Url: https://github.com/boostorg/phoenix/pull/94#issuecomment-602272355  

LGTM. Waiting for CI to cycle (asuming it will :-)

---

## Comment 2

> Username: mgaunard  
> Created_at: 2020-04-06 09:22:45 UTC  
> Url: https://github.com/boostorg/phoenix/pull/94#issuecomment-609678072  

Can we get this in 1.73 please?

---

## Comment 3

> Username: Kojoley  
> Created_at: 2020-04-06 09:28:28 UTC  
> Url: https://github.com/boostorg/phoenix/pull/94#issuecomment-609681182  

@mgaunard Your changes look reasonable, but could you maybe add some tests that show asan fails before the PR? I have added asan to the CI and it currently did not find issues, and I have not come with a reproducer myself.

---

## Comment 4

> Username: mgaunard  
> Created_at: 2020-04-07 12:32:20 UTC  
> Updated_at: 2020-04-07 12:32:37 UTC  
> Url: https://github.com/boostorg/phoenix/pull/94#issuecomment-610359150  

I do not think I will have the time to do a proper one before tomorrow the deadline for boost 1.73.  
  
ASan is a tool that can help in finding bugs, it certainly cannot act as a control to check whether bugs are present or not, so I'm not sure what value you're expecting to get there.  
The bug itself is quite obvious from a glance at the code, and to be honest I'm quite concerned this went past the review process.  
  
try_catch itself has no tests as-is.  
I suppose an easy example would be something like this (not tested)  
  
```  
#include <boost/phoenix.hpp>  
#include <cassert>  
  
auto make_func()  
{  
    namespace phoenix = boost::phoenix;  
    using namespace phoenix::placeholders;  
    return phoenix::try_[phoenix::throw_(arg1)].catch_all[phoenix::ref(arg2) = true];  
}  
  
int main()  
{  
    auto const f = make_func();  
  
    bool thrown = false;  
    f(42, thrown);  
    assert(thrown == true);  
}  
```  
Clearly the "throw arg1" and "arg2 = true" sub-expressions don't exist anymore when f is called. I made it into a separate function returning another function to increase the chances of ASan detecting it.

---

## Comment 5

> Username: djowel  
> Created_at: 2020-04-08 09:33:11 UTC  
> Url: https://github.com/boostorg/phoenix/pull/94#issuecomment-610855431  

I'll have this merged. Let's deal with the test later.

---
