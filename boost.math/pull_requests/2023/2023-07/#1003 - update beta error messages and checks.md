# #1003 update beta error messages and checks [Merged]

> Username: ryanelandt  
> Created at: 2023-07-25 20:41:33 UTC  
> Updated at: 2023-07-31 14:10:55 UTC  
> Merged at: 2023-07-31 13:54:34 UTC  
> Closed at: 2023-07-31 13:54:34 UTC  
> Url: https://github.com/boostorg/math/pull/1003  

This is chunk 2 for #1000.  
  
**Runtime errors**  
One approach to run-time errors takes the form:  
```  
if(!(boost::math::isfinite)(a))  
   return policies::raise_domain_error<T>(function, "a is bad because reason (got a=%1%).", a, pol);  
```  
When using this approach, it's understood that the error message should agree with the check that is actually being performed.  
  
**This PR**  
This PR fixes 6 places where the check and stated error message lacked agreement in the `beta.hpp` file. These incorrect error messages make debugging the Newton solver take longer than it would have otherwise.  
  
**Ideas about future solutions**  
Disagreement between the check and the error message are too easy to make. I think a better solution on a high level would to be able to use the BOOST_TEST types macros for run-time checks. I don't know if this kind of thing is possible. But either way, that's out of the scope of this PR.

---

## Comment 1

> Username: mborland  
> Created_at: 2023-07-31 13:54:09 UTC  
> Url: https://github.com/boostorg/math/pull/1003#issuecomment-1658421289  

LGTM. You are correct that there are probably other places where other incorrect error messages are lingering. Thanks.

---

## Comment 2

> Username: ryanelandt  
> Created_at: 2023-07-31 14:10:51 UTC  
> Url: https://github.com/boostorg/math/pull/1003#issuecomment-1658451088  

Thanks!

---
