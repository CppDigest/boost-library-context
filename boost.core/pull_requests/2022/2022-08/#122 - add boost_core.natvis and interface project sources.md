# #122 add boost_core.natvis and interface project sources [Merged]

> Username: vinniefalco  
> Created at: 2022-08-19 19:20:52 UTC  
> Updated at: 2023-01-12 20:19:53 UTC  
> Merged at: 2023-01-12 20:19:53 UTC  
> Closed at: 2023-01-12 20:19:53 UTC  
> Url: https://github.com/boostorg/core/pull/122  

moved the file to extra/ and tidied up the CML

---

## Comment 1

> Username: glenfe  
> Created_at: 2022-08-19 20:48:30 UTC  
> Url: https://github.com/boostorg/core/pull/122#issuecomment-1221081755  

Looks OK to me.

---

## Comment 2

> Username: Lastique  
> Created_at: 2022-08-20 16:07:58 UTC  
> Url: https://github.com/boostorg/core/pull/122#issuecomment-1221356649  

I'm ok too.

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-08-20 16:59:00 UTC  
> Url: https://github.com/boostorg/core/pull/122#issuecomment-1221367078  

I'll merge this - eventually - but I want to think about the CML part a bit first.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2022-10-27 23:21:48 UTC  
> Url: https://github.com/boostorg/core/pull/122#issuecomment-1294226058  

Any thoughts on this?

---

## Comment 5

> Username: pdimov  
> Created_at: 2022-10-27 23:37:03 UTC  
> Url: https://github.com/boostorg/core/pull/122#issuecomment-1294235028  

I'm sitting on all these because while the .natvis files can be added immediately, I don't want to make the CMakeLists.txt changes in this manner because it will make them not match the `boostdep --cmake` output so they'll need to be maintained by hand from that point on, and I'd like to avoid that.  
  
I was going to pick and merge the .natvis files by hand but I'm busy with other things now.

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2022-10-27 23:38:10 UTC  
> Url: https://github.com/boostorg/core/pull/122#issuecomment-1294235608  

Can boostdep integrate .natvis files into the CML if it sees them?

---
