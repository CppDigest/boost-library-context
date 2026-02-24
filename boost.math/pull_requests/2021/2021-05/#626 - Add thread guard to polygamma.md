# #626 Add thread guard to polygamma [Merged]

> Username: mborland  
> Created at: 2021-05-09 16:01:58 UTC  
> Updated at: 2021-05-10 17:39:00 UTC  
> Merged at: 2021-05-10 16:56:34 UTC  
> Closed at: 2021-05-10 16:56:34 UTC  
> Url: https://github.com/boostorg/math/pull/626  

Continued work on issue #621. The call to `std::mutex` and `std::lock_guard` were already wrapped so only the header inclusion needed changed. Also make the arrays `constexpr`

---

## Comment 1

> Username: mborland  
> Created_at: 2021-05-10 15:26:21 UTC  
> Url: https://github.com/boostorg/math/pull/626#issuecomment-836837785  

@jzmaddock Pending review this should be good to go.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-05-10 16:56:28 UTC  
> Url: https://github.com/boostorg/math/pull/626#issuecomment-836964292  

Cheers Matt!

---
