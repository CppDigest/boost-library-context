# #460 X3: Replaced make_attribute with direct attribute construction [Merged]

> Username: Kojoley  
> Created at: 2019-02-12 16:18:25 UTC  
> Updated at: 2019-02-18 17:25:57 UTC  
> Merged at: 2019-02-18 17:25:55 UTC  
> Closed at: 2019-02-18 17:25:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/460  

If the attribute has a general way to be constructed it should already have a  
default constructor.

---

## Comment 1

> Username: djowel  
> Created_at: 2019-02-12 22:22:36 UTC  
> Updated_at: 2019-02-12 22:25:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/460#issuecomment-462960945  

These are customization points. There's a corresponding CP in Qi, again IIRC. I don't think this should be removed.  
  
@hkaiser please correct me if I'm wrong.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2019-02-12 22:36:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/460#issuecomment-462965689  

> These are customization points.  
  
`make_attribute` is not advertised as a customization point anywhere and it has a strange signature to be one of.  
  
> There's a corresponding CP in Qi  
  
Yes there is the same thing in Qi and there are also questions about it (Karma is behaving weird IIRC, it does not pass-through attributes, something related to const problems), but I am not in the mood to touch Qi/Karma.

---

## Comment 3

> Username: djowel  
> Created_at: 2019-02-12 22:42:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/460#issuecomment-462967732  

Good point. Thoughts, @hkaiser ?

---

## Comment 4

> Username: Kojoley  
> Created_at: 2019-02-12 22:42:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/460#issuecomment-462967855  

If you want to know my point on Qi/Karma `make_attribute` - it should be merged into `transform_attribute`, it will solve symmetric transformation (possible, but X3 had some) problems.

---

## Comment 5

> Username: Kojoley  
> Created_at: 2019-02-17 00:40:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/460#issuecomment-464403234  

Ping?

---

## Comment 6

> Username: Kojoley  
> Created_at: 2019-02-18 14:55:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/460#issuecomment-464761225  

@hkaiser seems to be active on GitHub but does not answer here :-(

---

## Comment 7

> Username: hkaiser  
> Created_at: 2019-02-18 16:37:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/460#issuecomment-464802944  

@Kojoley I don't know anything about X3, so I'd rather not say anything.

---

## Comment 8

> Username: Kojoley  
> Created_at: 2019-02-18 16:40:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/460#issuecomment-464804043  

> @Kojoley I don't know anything about X3, so I'd rather not say anything.  
  
There is #464 for Qi/Karma with 'backported' changes.

---
