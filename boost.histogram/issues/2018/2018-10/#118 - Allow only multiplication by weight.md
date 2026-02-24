# #118 - Allow only multiplication by weight? [Closed]

> Username: HDembinski  
> Created at: 2018-10-08 07:17:14 UTC  
> Updated at: 2019-01-24 21:04:50 UTC  
> Closed at: 2019-01-24 21:04:05 UTC  
> Url: https://github.com/boostorg/histogram/issues/118  

Steven:  
> I wonder whether you could handle the issue that  
h * 2 != h + h, by using h * weight(2) to make it  
clear that multiplication changes the weights of  
the samples instead of changing the number of samples.

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-01-24 21:04:04 UTC  
> Updated at: 2019-01-24 21:04:50 UTC  
> Url: https://github.com/boostorg/histogram/issues/118#issuecomment-457355660  

While multiplying by weight would be more clear, it is less intuitive for the potential users of this feature, who are used to do multiplication by real numbers. The issue has also been pushed out of sight of the average user who does not care about weights, since the default storage does not handle weights in a non-trivial way any more. Users now need to explicitly build a histogram with a weight_storage to get the surprising behaviour, and hopefully then they are not surprised by it.
