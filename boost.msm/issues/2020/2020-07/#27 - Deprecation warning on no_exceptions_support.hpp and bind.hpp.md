# #27 - Deprecation warning on no_exceptions_support.hpp and bind.hpp [Closed]

> Username: jefftrull  
> Created at: 2020-07-28 03:06:21 UTC  
> Updated at: 2020-10-23 13:09:29 UTC  
> Closed at: 2020-10-23 13:09:29 UTC  
> Url: https://github.com/boostorg/msm/issues/27  

This header has moved to Boost `core` and so is now producing deprecation warnings at its use [here](https://github.com/boostorg/msm/blob/d60692b6f9750d147e7587a5cdcbc735ca141d31/include/boost/msm/back/state_machine.hpp#L20).

---

## Comment 1

> Username: jefftrull  
> Created at: 2020-07-28 03:07:57 UTC  
> Url: https://github.com/boostorg/msm/issues/27#issuecomment-664749474  

Also `bind.hpp` is in a new spot so [this](https://github.com/boostorg/msm/blob/d60692b6f9750d147e7587a5cdcbc735ca141d31/include/boost/msm/back/state_machine.hpp#L44) should be edited.
