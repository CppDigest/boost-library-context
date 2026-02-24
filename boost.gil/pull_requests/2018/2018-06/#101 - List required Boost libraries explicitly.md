# #101 [travis] List required Boost libraries explicitly [Merged]

> Username: mloskot  
> Created at: 2018-06-22 19:04:54 UTC  
> Updated at: 2018-06-22 21:51:42 UTC  
> Merged at: 2018-06-22 21:51:39 UTC  
> Closed at: 2018-06-22 21:51:39 UTC  
> Url: https://github.com/boostorg/gil/pull/101  

(NOTE: _Assigned to self - if accepted, I'd like to merge myself. Thanks._)  
  
### Description: what does this pull request do?  
  
`tools/boostdep/depinst/depinst.py` helper automatically calculates maximum set of required Boost libraries, based on `#include`-s.  
  
Manual listing should help to control any new additions and prevent introduction of unwanted dependencies.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Review 1 [Approved]

> Username: stefanseefeld  
> Created_at: 2018-06-22 19:15:14 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/101#pullrequestreview-131315285  

Excellent idea ! Then we will feel good each time we can remove one of these lines. :-)

📁 .travis.yml

```diff
 179 |   - ./bootstrap.sh
 160 |-   - ./b2 headers
 180 |+   # - ./b2 headers REQUIRED?
```

> Username: stefanseefeld  
> Created_at: 2018-06-22 19:14:31 UTC  
> Updated_at: 2018-06-22 20:13:52 UTC  
> Url: https://github.com/boostorg/gil/pull/101#discussion_r197543456  

With `b2` that's implicit, but I actually prefer it to be explicit (for similar reasons as your addition above), and it may also be needed for other build systems (cmake, faber), unless we handle include search paths explicitly there (which wouldn't be a bad thing either !)

> Username: mloskot  
> Created_at: 2018-06-22 19:51:12 UTC  
> Updated_at: 2018-06-22 20:13:52 UTC  
> Url: https://github.com/boostorg/gil/pull/101#discussion_r197552295  

Good. I've restored it.


---

## Comment 2

> Username: mloskot  
> Created_at: 2018-06-22 20:25:19 UTC  
> Updated_at: 2018-06-22 20:25:35 UTC  
> Url: https://github.com/boostorg/gil/pull/101#issuecomment-399571741  

@stefanseefeld Could you decide on the milestone?  
  
p.s. I have created [Boost 1.69.0](https://github.com/boostorg/gil/milestone/4) as the future

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2018-06-22 20:35:15 UTC  
> Url: https://github.com/boostorg/gil/pull/101#issuecomment-399574196  

I'm not sure how realistic it is to assign projects (or even issues) to actual releases. That's not a decision for me (or anyone else for that matter) to make.  
I still have a tiny bit of hope to get https://github.com/boostorg/gil/milestone/1 into the upcoming release, but merely assigning issues to releases doesn't magically resolve them. ;-)  
So, I prefer to use milestones independently of releases, or else we'll miss most of them (and then have the frustrating task of reassigning issues to new milestones...)

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-06-22 20:57:42 UTC  
> Url: https://github.com/boostorg/gil/pull/101#issuecomment-399579978  

I meant if you can assign as if you can decide if this should be merged now and be part of the grand merge or postponed.  
  
I also think it only makes sense to assign individual issues/pulls, not whole projects.  
  
Regarding milestones naming, I agree, but I just used Boost release as a name of closer or more distant point in time, not necessarily indicating a strict plan. No better name came up quick. The Boost 1.68 one has got date though but as a reminder :)

---

## Comment 5

> Username: stefanseefeld  
> Created_at: 2018-06-22 21:04:24 UTC  
> Url: https://github.com/boostorg/gil/pull/101#issuecomment-399581682  

I think this PR can go in right away. https://github.com/boostorg/gil/projects/3 however has much broader scope than what should go into https://github.com/boostorg/gil/milestone/1, IMO.

---

## Comment 6

> Username: mloskot  
> Created_at: 2018-06-22 21:21:10 UTC  
> Updated_at: 2018-06-22 21:23:28 UTC  
> Url: https://github.com/boostorg/gil/pull/101#issuecomment-399585512  

My understanding is that  projects do not have to follow milestones/releases but may overlap - deps cleanup may span across number of releases, isn't it. That also means the broader scope, AFAIU. So, I don't see any conflict between developing X as part of project and assigning it to milestone to indicate point of merge & release

---

## Comment 7

> Username: stefanseefeld  
> Created_at: 2018-06-22 21:21:59 UTC  
> Url: https://github.com/boostorg/gil/pull/101#issuecomment-399585721  

yes, precisely.

---
