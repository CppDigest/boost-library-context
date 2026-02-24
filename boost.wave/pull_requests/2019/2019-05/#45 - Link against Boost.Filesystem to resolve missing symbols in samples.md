# #45 Link against Boost.Filesystem to resolve missing symbols in samples [Merged]

> Username: jefftrull  
> Created at: 2019-05-03 15:08:40 UTC  
> Updated at: 2020-01-12 06:03:02 UTC  
> Merged at: 2020-01-12 06:03:01 UTC  
> Closed at: 2020-01-12 06:03:02 UTC  
> Url: https://github.com/boostorg/wave/pull/45  

I noticed several samples would fail at the link stage, all with missing Boost.Filesystem symbols. I couldn't find a way to have them inlined so I assume this changed at some point. Adding FS explicitly fixes it.

---

## Comment 1

> Username: jefftrull  
> Created_at: 2019-05-03 18:25:31 UTC  
> Url: https://github.com/boostorg/wave/pull/45#issuecomment-489194013  

Possibly related to [TRAC 6589](https://svn.boost.org/trac10/ticket/6589)

---

## Review 2 [Approved]

> Username: hkaiser  
> Created_at: 2019-05-03 23:04:34 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/45#pullrequestreview-233701837  

Thanks!

---
