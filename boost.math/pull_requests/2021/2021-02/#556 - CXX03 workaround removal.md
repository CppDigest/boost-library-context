# #556 CXX03 workaround removal. [Closed]

> Username: NAThompson  
> Created at: 2021-02-25 14:30:12 UTC  
> Updated at: 2023-02-21 17:35:30 UTC  
> Closed at: 2023-02-21 16:58:40 UTC  
> Url: https://github.com/boostorg/math/pull/556  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-02-25 14:50:42 UTC  
> Url: https://github.com/boostorg/math/pull/556#issuecomment-785950677  

FYI I can't seem to get boost.build to default to C++11, which might cause some pain with this transition.

---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2021-02-25 17:00:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/556#pullrequestreview-598793613  

📁 include/boost/math/special_functions/detail/bernoulli_details.hpp

```diff
 205 |          this->destroy(&m_data[i]);
 206 |       this->deallocate(m_data, m_capacity);
 208 |- #else
```

> Username: jzmaddock  
> Created_at: 2021-02-25 17:00:49 UTC  
> Url: https://github.com/boostorg/math/pull/556#discussion_r583004764  

I think you've deleted the wrong branch here?  
  
This will break C++17 as well I believe.


---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-02-25 17:04:28 UTC  
> Url: https://github.com/boostorg/math/pull/556#issuecomment-786055126  

Looks good apart from the one comment.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2021-02-25 17:15:24 UTC  
> Url: https://github.com/boostorg/math/pull/556#issuecomment-786062836  

I'm gonna leave this one until next release; clearly there are opportunities for silly mistakes.

---

## Comment 5

> Username: mborland  
> Created_at: 2023-02-21 16:58:40 UTC  
> Url: https://github.com/boostorg/math/pull/556#issuecomment-1438811154  

Some of these have been hit already, and the rest will be in https://github.com/boostorg/math/pull/956

---
