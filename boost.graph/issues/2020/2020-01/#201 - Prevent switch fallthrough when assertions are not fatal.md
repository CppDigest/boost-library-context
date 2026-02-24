# #201 - Prevent switch fallthrough when assertions are not fatal [Closed]

> Username: jwakely  
> Created at: 2020-01-31 14:46:04 UTC  
> Updated at: 2020-01-31 14:49:17 UTC  
> Closed at: 2020-01-31 14:49:17 UTC  
> Url: https://github.com/boostorg/graph/issues/201  

When BOOST_DISABLE_ASSERTS or BOOST_ENABLE_ASSERT_HANDLER is defined, the nested switch on `str[1]` can continue past the assert and will fallthrough to the `default` case below.  
  
This can cause warnings with static analysis tools, and if a user-defined boost::assertion_failed function that doesn't terminate the process is being used, that function will be called twice because the first assertion falls through to the second.  
  
Inserting a `break` avoids static analysis warnings and avoids potentially calling the assertion handler twice.

---

## Comment 1

> Username: jwakely  
> Created at: 2020-01-31 14:49:17 UTC  
> Url: https://github.com/boostorg/graph/issues/201#issuecomment-580763696  

I meant to submit a pull request and got confused about which browser tab I was typing in. Re-submitted as https://github.com/boostorg/graph/pull/202 instead.
