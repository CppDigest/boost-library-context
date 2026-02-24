# #375 Karma: Fixed use after scope bug if ADT getter returns by value [Merged]

> Username: Kojoley  
> Created at: 2018-03-13 23:57:59 UTC  
> Updated at: 2018-03-15 15:50:21 UTC  
> Merged at: 2018-03-15 15:50:18 UTC  
> Closed at: 2018-03-15 15:50:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/375  

The `extract_from_attribute` returns a reference to a temporary  
value produced by calling Fusion ADT adapted sequence getter that  
returns by value.  
  
Was reported 6 years ago https://svn.boost.org/trac10/ticket/6126

---

## Comment 1

> Username: Kojoley  
> Created_at: 2018-03-14 00:02:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/375#issuecomment-372859955  

I had to split default `extract_from_attribute` specialization because `fusion::result_of::at_c<Attribute, 0>` instantiation will fail if attribute is not a fusion sequence.

---

## Review 2 [Approved]

> Username: hkaiser  
> Created_at: 2018-03-14 00:09:46 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/spirit/pull/375#pullrequestreview-103656843  

LGTM, thanks!

---
