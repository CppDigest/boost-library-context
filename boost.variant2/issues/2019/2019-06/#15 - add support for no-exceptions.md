# #15 - add support for no-exceptions [Closed]

> Username: HDembinski  
> Created at: 2019-06-02 19:04:26 UTC  
> Updated at: 2019-06-03 14:25:51 UTC  
> Closed at: 2019-06-03 14:25:50 UTC  
> Url: https://github.com/boostorg/variant2/issues/15  

variant2 is using raw `throw` instead of `boost::throw_exception`, so it doesn't work with the `-fno-exceptions` compiler flag. It would be nice to support this, for example, Boost.Histogram needs this functionality to use variant2.

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-06-02 20:33:13 UTC  
> Url: https://github.com/boostorg/variant2/issues/15#issuecomment-498063149  

It looks like `throw_exception` needs constexpr support before it can be used in variant2. https://github.com/boostorg/throw_exception/blob/develop/include/boost/throw_exception.hpp

---

## Comment 2

> Username: pdimov  
> Created at: 2019-06-03 14:25:50 UTC  
> Url: https://github.com/boostorg/variant2/issues/15#issuecomment-498279130  

Should be fixed with https://github.com/boostorg/variant2/commit/f5d9662946774b51b7f353c190f980b1f2147bbd.
