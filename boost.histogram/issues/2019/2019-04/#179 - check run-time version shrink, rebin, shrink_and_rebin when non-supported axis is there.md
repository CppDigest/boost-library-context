# #179 - check run-time version shrink, rebin, shrink_and_rebin when non-supported axis is there [Closed]

> Username: HDembinski  
> Created at: 2019-04-12 12:29:15 UTC  
> Updated at: 2019-04-26 21:33:50 UTC  
> Closed at: 2019-04-26 21:33:50 UTC  
> Url: https://github.com/boostorg/histogram/issues/179  



---

## Comment 1

> Username: henryiii  
> Created at: 2019-04-22 16:16:23 UTC  
> Url: https://github.com/boostorg/histogram/issues/179#issuecomment-485463590  

I believe this could be done rather easily, by splitting the visitor on the axis into two separate functions, one that performs the action on a valid axis, and one that throws an error on an invalid axis.

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-04-24 10:38:35 UTC  
> Url: https://github.com/boostorg/histogram/issues/179#issuecomment-486169578  

This needs some TMP magic to detect the presence of the special constructor that enables the support for shrink, rebin and so on.

---

## Comment 3

> Username: henryiii  
> Created at: 2019-04-24 10:43:53 UTC  
> Url: https://github.com/boostorg/histogram/issues/179#issuecomment-486171188  

Yes, that's what selects which function to instantiate in the visitor. I already have a rough draft that works, but have had issues with the other bugs that I've mentioned. I'm also using `true_type` and `false_type`, rather than a more elegant method, since there are several ways to do it and I'm not sure which is recommended for Boost::Histogram for this sort of selection.

---

## Comment 4

> Username: HDembinski  
> Created at: 2019-04-24 14:30:55 UTC  
> Url: https://github.com/boostorg/histogram/issues/179#issuecomment-486266813  

Using tag types is fine.

---

## Comment 5

> Username: HDembinski  
> Created at: 2019-04-24 14:32:41 UTC  
> Url: https://github.com/boostorg/histogram/issues/179#issuecomment-486267545  

static_if may also be handy

---

## Comment 6

> Username: henryiii  
> Created at: 2019-04-24 14:55:55 UTC  
> Url: https://github.com/boostorg/histogram/issues/179#issuecomment-486277311  

Tag types require the caller to do the check. It's nicer if the caller doesn't have to know about it. (since it's a detail function it's not too bad).

---

## Comment 7

> Username: HDembinski  
> Created at: 2019-04-25 09:06:43 UTC  
> Url: https://github.com/boostorg/histogram/issues/179#issuecomment-486584066  

I don't know whether we talk about the same thing, by tag type I mean an extra argument to function, to trigger the right overload. That can be std::true_type or std::false_type. In any case, I am looking into this. This seems to be a subtle problem. It could be related, however, to some other errors you experienced where you asked me whether some axis type is not copy constructible. There may be a bug in the axis::variant which is causing this.

---

## Comment 8

> Username: henryiii  
> Created at: 2019-04-25 09:10:38 UTC  
> Url: https://github.com/boostorg/histogram/issues/179#issuecomment-486585496  

Yes, that's what I meant. The downside is the caller has to pass the flag type as an extra argument to the function, rather than just having it act on the types already present in the function signature. But it's simple and since it's private, is not a bad solution, just not as elegant as I would have liked. I'm currently setting up tag types to turn on/off weighted fills based on the storage.

---

## Comment 9

> Username: HDembinski  
> Created at: 2019-04-25 14:34:54 UTC  
> Url: https://github.com/boostorg/histogram/issues/179#issuecomment-486698712  

There is no better solution than this if you need to do static dispatch to methods.

---

## Comment 10

> Username: HDembinski  
> Created at: 2019-04-25 14:35:43 UTC  
> Url: https://github.com/boostorg/histogram/issues/179#issuecomment-486699047  

With `static_if` you can do it without adding private methods, but it has other drawbacks.

---

## Comment 11

> Username: HDembinski  
> Created at: 2019-04-26 21:33:50 UTC  
> Url: https://github.com/boostorg/histogram/issues/179#issuecomment-487208552  

fixed on develop
