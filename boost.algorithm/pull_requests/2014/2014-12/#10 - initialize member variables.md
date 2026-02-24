# #10 initialize member variables [Closed]

> Username: tempoz  
> Created at: 2014-12-08 20:47:59 UTC  
> Updated at: 2015-03-18 15:40:43 UTC  
> Closed at: 2015-03-18 15:40:43 UTC  
> Url: https://github.com/boostorg/algorithm/pull/10  

m_Next and m_End are not objects, so do not have constructors, and so are not initialized without explicit value initialization.  
Also moved the value initialization of m_bEof into the initializer list for consistency's sake.  
This was uncovered by Coverity issue CID10557.

---

## Comment 1

> Username: mclow  
> Created_at: 2014-12-08 20:50:58 UTC  
> Url: https://github.com/boostorg/algorithm/pull/10#issuecomment-66184770  

This looks good to me, but we don't pull into 'master'. We pull into 'develop', let the tests cycle, and then merge into 'master'.

---

## Comment 2

> Username: tempoz  
> Created_at: 2015-03-13 20:09:23 UTC  
> Url: https://github.com/boostorg/algorithm/pull/10#issuecomment-79328064  

Sooo, should i move the pull request there?

---

## Comment 3

> Username: mclow  
> Created_at: 2015-03-18 15:40:43 UTC  
> Url: https://github.com/boostorg/algorithm/pull/10#issuecomment-83025459  

I manually applied this against the develop branch in ba1894bfde9c5ec981e39c8892c8e8a7e6eab376.  
Please watch the tests, and ping me in about a few days, and I will merge to master, so this can be part of the 1.58.0 release.

---
