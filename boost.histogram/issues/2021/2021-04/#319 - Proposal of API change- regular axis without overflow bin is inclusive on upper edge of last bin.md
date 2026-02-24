# #319 - Proposal of API change: regular axis without overflow bin is inclusive on upper edge of last bin [Closed]

> Username: HDembinski  
> Created at: 2021-04-09 15:33:21 UTC  
> Updated at: 2021-09-30 12:59:27 UTC  
> Closed at: 2021-09-30 12:59:27 UTC  
> Url: https://github.com/boostorg/histogram/issues/319  

The proposal is to make the upper edge of the last bin of a regular axis inclusive, but only if overflow is off. One could argue that this is closer to what the user expects. It would address #307 in a natural way and would make the NumpyAxis in boost-histogram obsolete. It would also mean that we do not have to introduce a special axis for probabilities, where you do not need overflow bins, but you can certainly have probabilities equal to 1, which you want to include in the last bin.  
  
This is - strictly speaking - a breaking change since code that used such a regular axis would produce different results once this is changed, but it is a corner case. If you used a regular axis without overflow and you have values that end up exactly on the upper edge of the last bin, would you not want them to be included? I cannot imagine a scenario where the intent is to exclude them. So this could be regarded as a "fix" rather than a breaking change.  
  
@henryiii @jpivarski ?

---

## Comment 1

> Username: henryiii  
> Created at: 2021-04-23 14:03:55 UTC  
> Updated at: 2021-04-23 14:04:26 UTC  
> Url: https://github.com/boostorg/histogram/issues/319#issuecomment-825681920  

This felt weird at first, but I actually think it's not a bad idea at all. In fact, it's exactly what numpy does. If you do `..., 1]` for your edges, then exactly "1" falls in the final bin. But if you do `..., 1, np.infty]`, then exactly one falls in the "overflow" bin instead (since it's not the upper bin anymore). So once could rationalize that the final bin edge is included, but if you have overflow, then the final bin edge is +infinity (which is _already_ included, actually). +1 from me.  
  
Obviously I also like being able to dump our special NumPy axis code. ;)

---

## Comment 2

> Username: HDembinski  
> Created at: 2021-04-24 08:09:54 UTC  
> Url: https://github.com/boostorg/histogram/issues/319#issuecomment-826055245  

@henryiii Thank you for the feedback! As you said, it is not necessary to use the same logic when overflow is present, which is good because introducing the special case for the upper edge of the last bin is going to make the code a bit slower, I think.

---

## Comment 3

> Username: HDembinski  
> Created at: 2021-04-24 08:11:41 UTC  
> Updated at: 2021-04-24 08:11:49 UTC  
> Url: https://github.com/boostorg/histogram/issues/319#issuecomment-826055465  

I am pretty sure that the feature itself makes a lot of sense and it would actually avoid mistakes and surprises on the user end. My concern is mainly about the potential change in behavior. In Boost, the expectation on API stability is very high, that includes how the API behaves.
