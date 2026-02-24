# #45 - Should BOOST_CHRONO_VERSION be set to 2 by default? [Open]

> Username: inetic  
> Created at: 2019-09-24 10:36:35 UTC  
> Updated at: 2022-02-19 19:27:03 UTC  
> Url: https://github.com/boostorg/chrono/issues/45  

The [documentation says](https://github.com/boostorg/chrono/blob/2506383708ff7b14739dce8ec1760946209d8e70/doc/chrono.qbk#L2599) it should have been set to 2 since Boost 1.55. But as of writing this issue the default value is still [set to 1](https://github.com/boostorg/chrono/blob/2506383708ff7b14739dce8ec1760946209d8e70/include/boost/chrono/config.hpp#L19).

---

## Comment 1

> Username: viboes  
> Created at: 2020-01-28 21:27:10 UTC  
> Url: https://github.com/boostorg/chrono/issues/45#issuecomment-579464205  

Unfortunately, this migration path was not accepted as introduced breaking changes.  
  
I have not had the courage to implement a V2 independent from the V1 :(  
  
If some one can do it, please go ahead.

---

## Comment 2

> Username: tan-wei  
> Created at: 2021-01-09 04:28:35 UTC  
> Url: https://github.com/boostorg/chrono/issues/45#issuecomment-757093766  

IMO, documents should be modified. It leads misunderstand now.

---

## Comment 3

> Username: jefftrull  
> Created at: 2022-02-19 19:27:03 UTC  
> Url: https://github.com/boostorg/chrono/issues/45#issuecomment-1046088416  

I just got burned by this :( Please update the documentation, thank you.
