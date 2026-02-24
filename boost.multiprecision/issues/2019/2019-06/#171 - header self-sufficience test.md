# #171 - header self-sufficience test [Closed]

> Username: ohhmm  
> Created at: 2019-06-08 11:56:45 UTC  
> Updated at: 2019-11-24 12:14:43 UTC  
> Closed at: 2019-11-24 12:14:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/171  

self sufficient header is a header which requires no other headers to be included before the header.  
the example of an introduced issue because of introduced header non-self-sufficiency is multiprecision/roots.hpp in 1.70  
an automated test of boost headers self-sufficiency would scan all boost headers and compile its inclusion lines. if compilation successful then the header test passed well.

---

## Comment 1

> Username: NAThompson  
> Created at: 2019-11-19 17:54:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/171#issuecomment-555628971  

@ohhmm : Could you reopen this issue against boost.multiprecision?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-11-24 12:14:22 UTC  
> Updated at: 2019-11-24 12:14:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/171#issuecomment-557882894  

Duplicates #169.
