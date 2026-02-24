# #103 - [Monoid] Missing Monoids - Sum/Prod/All/Any/Dual/... [Closed]

> Username: viboes  
> Created at: 2015-06-13 13:54:38 UTC  
> Updated at: 2015-06-14 14:15:57 UTC  
> Closed at: 2015-06-14 12:02:02 UTC  
> Url: https://github.com/boostorg/hana/issues/103  

I'm missing the classical monoids Sum/Prod/All/Any/Dual/...  
  
Do you plan to add them?

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-13 19:54:28 UTC  
> Url: https://github.com/boostorg/hana/issues/103#issuecomment-111743381  

No, I don't plan adding them. While idiomatic Hana is pretty functional, I don't want the library itself to be a functional programming library. It is a metaprogramming library, and it turns out that the most general concepts come from functional programming, but the resemblance ends there.  
  
Also, from a practical standpoint, Hana only provides `plus`, `zero` and `sum` that work with any Monoid. Adding these Monoids would therefore not add much value to the library, at least not as I see it right now. However, if someone wanted to build a functional programming library based on Hana's concepts, he could definitely provide those Monoids as an extension to Hana. This could be useful if such a FP library defines more algorithms that work generically on any Monoid, unlike Hana which only provides the strict minimum.  
  
Now, my answer might leave you thinking: "Why define a Monoid concept in the first place, if you're not willing to use it?" The answer is that I wanted a _sound_ algebraic hierarchy that is more fine-grained than a simple `Arithmetic` concept, so that users can then use this hierarchy as they wish.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-06-13 19:56:41 UTC  
> Url: https://github.com/boostorg/hana/issues/103#issuecomment-111743504  

Do you have concrete use cases for these Monoids, in the context of metaprogramming with Hana? If so, I'll be more tempted to add them. But otherwise, I see it mostly as a feature that only a few FP people would use, whereas Hana is really aimed at the mainstream C++ programmer. That mainstream programmer has no use for the Dual Monoid.

---

## Comment 3

> Username: viboes  
> Created at: 2015-06-14 12:02:02 UTC  
> Url: https://github.com/boostorg/hana/issues/103#issuecomment-111816446  

Well, who know what is the strict necessary?  
  
Maybe you are right, and we don't need them for meta-programming, as you have already defined the function sum/prod/minimum/maximum in Foldable.

---

## Comment 4

> Username: ldionne  
> Created at: 2015-06-14 14:15:57 UTC  
> Url: https://github.com/boostorg/hana/issues/103#issuecomment-111829827  

I think we should stick to the minimum for now, and we can always add them to the library if/when we have use cases. I think the lack of use cases right now might be caused by the unsophistication of our view of template metaprogramming. However, we might discover much higher-level ways of expressing things if we have better tools. If that happens, I'll be happy to provide more useful features in the library.
