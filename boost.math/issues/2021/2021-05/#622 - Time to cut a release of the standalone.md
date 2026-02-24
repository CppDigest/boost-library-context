# #622 - Time to cut a release of the standalone? [Closed]

> Username: NAThompson  
> Created at: 2021-05-03 13:16:08 UTC  
> Updated at: 2021-05-03 23:09:11 UTC  
> Closed at: 2021-05-03 23:09:11 UTC  
> Url: https://github.com/boostorg/math/issues/622  

The releases workflow from github seems incredibly easy to use-I'd like to cut a standalone release after @mborland's incredibly great work on this topic.  
  
Do we have anything we'd like to do before doing so?

---

## Comment 1

> Username: mborland  
> Created at: 2021-05-03 16:26:28 UTC  
> Url: https://github.com/boostorg/math/issues/622#issuecomment-831376091  

I can't think of anything else that needs to go in. I think it would be useful to put out in the user ML and/or dev ML that a standalone beta is available. I am sure that will turn up stuff that may have been missed.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-05-03 16:59:42 UTC  
> Url: https://github.com/boostorg/math/issues/622#issuecomment-831394939  

>I can't think of anything else that needs to go in. I think it would be useful to put out in the user ML and/or dev ML that a standalone beta is available. I am sure that will turn up stuff that may have been missed.  
  
+1  
  
Other than that, at some point I would like to solve the build-on-msvc-clr issues and/or other platforms with no threading support.

---

## Comment 3

> Username: NAThompson  
> Created at: 2021-05-03 17:28:57 UTC  
> Url: https://github.com/boostorg/math/issues/622#issuecomment-831411541  

Done!  
  
Give it a try when you have a minute.  
  
Also, my messages always get bounced from the mailing list, so please post  message for me.

---

## Comment 4

> Username: NAThompson  
> Created at: 2021-05-03 17:29:21 UTC  
> Url: https://github.com/boostorg/math/issues/622#issuecomment-831411747  

@ax3l : You should now be able to vendorize boost.math in isolation.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2021-05-03 17:49:58 UTC  
> Url: https://github.com/boostorg/math/issues/622#issuecomment-831423061  

>https://github.com/boostorg/math/releases/tag/v1.76-standalone-beta  
  
Done, let me know what email address is getting bounced and I'll investigate.

---

## Comment 6

> Username: mborland  
> Created at: 2021-05-03 18:25:26 UTC  
> Url: https://github.com/boostorg/math/issues/622#issuecomment-831444383  

> Other than that, at some point I would like to solve the build-on-msvc-clr issues and/or other platforms with no threading support.  
  
I can go through my tree and find places I dropped paths based on having threads of not. Replicating `BOOST_HAS_THREADS`  for standalone mode should not be overly difficult. Adding support for metal platforms may be a different story.

---

## Comment 7

> Username: NAThompson  
> Created at: 2021-05-03 19:51:19 UTC  
> Url: https://github.com/boostorg/math/issues/622#issuecomment-831493281  

Release is cut, closing.
