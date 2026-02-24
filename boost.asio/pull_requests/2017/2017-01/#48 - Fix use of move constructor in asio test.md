# #48 Fix use of move constructor in asio test [Closed]

> Username: visigoth  
> Created at: 2017-01-14 19:57:35 UTC  
> Updated at: 2017-12-02 07:35:34 UTC  
> Closed at: 2017-12-02 07:35:34 UTC  
> Url: https://github.com/boostorg/asio/pull/48  

L63 is undefined per the spec. This change adjusts the test to something that makes more sense, moving descriptor1 into descriptor3 and then back to descriptor1.

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2017-12-02 07:35:34 UTC  
> Url: https://github.com/boostorg/asio/pull/48#issuecomment-348674970  

Fixed on develop.

---
