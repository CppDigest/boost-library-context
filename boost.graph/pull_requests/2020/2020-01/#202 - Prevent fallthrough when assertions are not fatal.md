# #202 Prevent fallthrough when assertions are not fatal [Merged]

> Username: jwakely  
> Created at: 2020-01-31 14:48:43 UTC  
> Updated at: 2021-04-20 18:28:50 UTC  
> Merged at: 2021-04-20 18:11:58 UTC  
> Closed at: 2021-04-20 18:11:58 UTC  
> Url: https://github.com/boostorg/graph/pull/202  

When BOOST_DISABLE_ASSERTS or BOOST_ENABLE_ASSERT_HANDLER is defined, the nested switch on `str[1]` can continue past the assert and will fallthrough to the `default` case below.  
  
This can cause warnings with static analysis tools, and if a user-defined `boost::assertion_failed` function that doesn't terminate the process is being used, that function will be called twice because the first assertion falls through to the second.  
  
Inserting a `break` avoids static analysis warnings and avoids potentially calling the assertion handler twice.

---
