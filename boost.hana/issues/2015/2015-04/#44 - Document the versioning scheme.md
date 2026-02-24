# #44 - Document the versioning scheme [Closed]

> Username: ldionne  
> Created at: 2015-04-17 16:02:37 UTC  
> Updated at: 2015-04-23 19:18:55 UTC  
> Closed at: 2015-04-22 20:42:21 UTC  
> Url: https://github.com/boostorg/hana/issues/44  

We're currently in v0.1 and following the version scheme of [Semantic Versioning](http://semver.org), but this should be documented and detailed more precisely.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-04-22 20:42:21 UTC  
> Url: https://github.com/boostorg/hana/issues/44#issuecomment-95328651  

Closed by 2f356a8. I suck at closing issues automatically from commits; I keep on doing typos.

---

## Comment 2

> Username: HaMster21  
> Created at: 2015-04-23 10:41:44 UTC  
> Url: https://github.com/boostorg/hana/issues/44#issuecomment-95531543  

I guess you're a human then :wink:   
  
:+1: for using semver

---

## Comment 3

> Username: ldionne  
> Created at: 2015-04-23 13:07:29 UTC  
> Url: https://github.com/boostorg/hana/issues/44#issuecomment-95578106  

I have no experience with this versioning system; we'll see how it goes. I am under the impression that I'll hit release 32.0.0 under no time, but I guess that'll motivate me to consider API-breaking changes more seriously :-).

---

## Comment 4

> Username: HaMster21  
> Created at: 2015-04-23 19:18:55 UTC  
> Url: https://github.com/boostorg/hana/issues/44#issuecomment-95690130  

I try to follow along since your talk at CppCon and at them moment I don't think that your are breaking your public API constantly. And you are in the middle of development anyways, so your API version would be some 0.x version. Breaking your public API becomes a serious issue if you publish a first version that you consider to be a **stable** 1.0.0 :wink:   
  
As long as you are adding functionality, just change the 0.x.y parts of the version, maybe with an additional _+ci-travis-[Travis build number]_ for a CI build if that ever goes public.
