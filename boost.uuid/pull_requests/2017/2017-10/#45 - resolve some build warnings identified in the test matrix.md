# #45 resolve some build warnings identified in the test matrix [Merged]

> Username: jeking3  
> Created at: 2017-10-14 12:02:15 UTC  
> Updated at: 2017-10-18 19:52:35 UTC  
> Merged at: 2017-10-15 02:06:06 UTC  
> Closed at: 2017-10-15 02:06:06 UTC  
> Url: https://github.com/boostorg/uuid/pull/45  



---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2017-10-14 20:13:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/45#pullrequestreview-69401177  

📁 include/boost/uuid/uuid_io.hpp

```diff
  23 |- #if defined(_MSC_VER)
  24 |- #pragma warning(push) // Save warning settings.
  25 |- #pragma warning(disable : 4996) // Disable deprecated std::ctype<char>::widen, std::copy
```

> Username: Lastique  
> Created_at: 2017-10-14 20:13:48 UTC  
> Updated_at: 2017-10-14 21:51:53 UTC  
> Url: https://github.com/boostorg/uuid/pull/45#discussion_r144702521  

By removing this code you enable warnings in the users' code.

> Username: jeking3  
> Created_at: 2017-10-14 20:27:51 UTC  
> Updated_at: 2017-10-14 21:51:53 UTC  
> Url: https://github.com/boostorg/uuid/pull/45#discussion_r144702838  

This was done intentionally.  I receive zero warnings on msvc-10.0 through 14.1 in the unit tests for uuid (I built with warnings-as-errors to be sure).  Perhaps there is a missing unit test.  
  
I see in other libraries such as random, it is up to the build command (see random/Jamfile.v2) to disable these warnings by defining `_SCL_SECURE_NO_WARNINGS` and not up to the code.  
  
If the regression matrix for uuid shows no warnings, do we still need to disable 4996?

> Username: Lastique  
> Created_at: 2017-10-14 20:35:57 UTC  
> Updated_at: 2017-10-14 21:51:53 UTC  
> Url: https://github.com/boostorg/uuid/pull/45#discussion_r144702983  

Disabling warnings in the Jamfile is only good when building the library (i.e. it is beneficial only when the warning is triggered by the source code that is never included by users). In headers the warnings should be suppressed in code.  
  
I cannot test MSVC now, so I don't know if the warning is triggered by tests on some other compiler version. But it seems not impossible that the tests don't trigger the warning. I can see both `widen` and `std::copy` are still used in the code.

> Username: jeking3  
> Created_at: 2017-10-14 21:49:36 UTC  
> Updated_at: 2017-10-14 21:51:53 UTC  
> Url: https://github.com/boostorg/uuid/pull/45#discussion_r144704491  

My point being anybody compiling with Boost.Random will need to do the same thing to disable the warnings as well, so I was trying to be uniform.  I see no pragmas to disable 4996 in random code anywhere.  I suspect this is intentional and it is up to the consuming build to deal with it?  
  
I can put this back though.  It doesn't hurt anything to leave it there.  It just didn't seem like it was necessary any more based on the (likely incomplete) tests.


---
