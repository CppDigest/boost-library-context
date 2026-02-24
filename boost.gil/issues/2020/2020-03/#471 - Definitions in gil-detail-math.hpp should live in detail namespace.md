# #471 - Definitions in gil/detail/math.hpp should live in detail namespace [Closed]

> Username: mloskot  
> Created at: 2020-03-31 20:29:29 UTC  
> Updated at: 2020-04-05 16:56:03 UTC  
> Closed at: 2020-04-05 16:55:06 UTC  
> Url: https://github.com/boostorg/gil/issues/471  

@simmplecoder I think these should be moved to `boost::gil::detail` namespace:  
  
https://github.com/boostorg/gil/blob/28a99d7bd761ea813590610179418c2f7c395439/include/boost/gil/detail/math.hpp#L18-L25  
  
They are not really part of the public interface, are they?

---

## Comment 1

> Username: simmplecoder  
> Created at: 2020-04-05 16:55:06 UTC  
> Url: https://github.com/boostorg/gil/issues/471#issuecomment-609447235  

I guess this is closed now?

---

## Comment 2

> Username: mloskot  
> Created at: 2020-04-05 16:56:03 UTC  
> Url: https://github.com/boostorg/gil/issues/471#issuecomment-609447379  

Yes, I forgot to close from the commit message. Thanks
