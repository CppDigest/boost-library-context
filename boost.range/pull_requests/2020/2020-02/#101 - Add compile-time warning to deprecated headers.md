# #101 Add compile-time warning to deprecated headers [Merged]

> Username: Kojoley  
> Created at: 2020-02-13 14:24:36 UTC  
> Updated at: 2020-08-10 23:41:47 UTC  
> Merged at: 2020-05-11 18:04:31 UTC  
> Closed at: 2020-05-11 18:04:31 UTC  
> Url: https://github.com/boostorg/range/pull/101  



---

## Comment 1

> Username: Romain-Geissler-1A  
> Created_at: 2020-08-10 23:41:47 UTC  
> Url: https://github.com/boostorg/range/pull/101#issuecomment-671643095  

Hi,  
  
This pull requests is the source of issue #106. The problem is, these top level headers are being included explicitly by the top-level file "boost/range.hpp". I suggest to also update "boost/range.hpp" to include the new files instead, and I would directly define the old names inside the new file, marking them as [[deprecated]] so that people get warnings when using the old names.  
  
Cheers,  
Romain

---
