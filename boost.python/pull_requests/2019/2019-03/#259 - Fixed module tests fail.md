# #259 Fixed module tests fail [Merged]

> Username: Kojoley  
> Created at: 2019-03-30 14:44:40 UTC  
> Updated at: 2019-04-01 00:37:49 UTC  
> Merged at: 2019-04-01 00:36:57 UTC  
> Closed at: 2019-04-01 00:36:57 UTC  
> Url: https://github.com/boostorg/python/pull/259  

Since recently lightweight_test introduced a sanitation to ensure that `boost::report_errors()` is called, and it does not in module tests.  
  
The problem could be fixed by exporting the function and calling it from these python tests, but as it already done in other module tests I just turned those usages lightweight_test to a regular assertation.  
  
ref #254

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2019-03-30 15:32:54 UTC  
> Updated_at: 2019-03-30 15:33:16 UTC  
> Url: https://github.com/boostorg/python/pull/259#issuecomment-478256554  

I don't think `BOOST_ASSERT` is a device meant to be used for testing. Notably, it will empty (and the enclosed expression won't be evaluated) if `NDEBUG` is defined, just like the standard `assert` macro.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2019-03-30 15:35:58 UTC  
> Updated_at: 2019-03-30 15:41:59 UTC  
> Url: https://github.com/boostorg/python/pull/259#issuecomment-478256831  

Then fix it like you think it should be.  
  
Also grep the tests for `BOOST_ASSERT`. https://github.com/boostorg/python/search?q=BOOST_ASSERT&unscoped_q=BOOST_ASSERT  
  
The cases closely related to this PR:  
https://github.com/boostorg/python/blob/ac62db1cf17c0af44dc2b2117f9ddeee327e5e7c/test/virtual_functions.cpp#L15-L34  
https://github.com/boostorg/python/blob/ac62db1cf17c0af44dc2b2117f9ddeee327e5e7c/test/back_reference.cpp#L24-L39  
https://github.com/boostorg/python/blob/ac62db1cf17c0af44dc2b2117f9ddeee327e5e7c/test/callbacks.cpp#L31-L46

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2019-04-01 00:36:50 UTC  
> Url: https://github.com/boostorg/python/pull/259#issuecomment-478400923  

OK, fair enough. I'll merge this in an attempt to "harmonize" our testing logic. Then we can open a new request to substitute `boost_assert` by something more robust and suitable for use with testing.

---
