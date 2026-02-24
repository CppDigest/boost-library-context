# #455 - Analog of hana::template_ for lifting non-type templates [Open]

> Username: invexed  
> Created at: 2019-07-31 06:34:14 UTC  
> Updated at: 2019-07-31 15:20:17 UTC  
> Url: https://github.com/boostorg/hana/issues/455  

Is an analog of ```hana::template_``` for lifting non-type templates within the scope of Boost.Hana? There is a naive implementation in my answer [here](https://stackoverflow.com/a/57284483/11817970).

---

## Comment 1

> Username: ldionne  
> Created at: 2019-07-31 14:51:26 UTC  
> Url: https://github.com/boostorg/hana/issues/455#issuecomment-516883614  

Yes, it is definitely in scope. It's simply that we don't currently use C++17 features, however we could/should.

---

## Comment 2

> Username: invexed  
> Created at: 2019-07-31 15:00:41 UTC  
> Updated at: 2019-07-31 15:20:17 UTC  
> Url: https://github.com/boostorg/hana/issues/455#issuecomment-516887732  

I see. A while ago there was some talk about a Hana 2.0, with breaking changes. Is that still in the pipeline? If so, I imagine that would be a good time to incorporate the above?
