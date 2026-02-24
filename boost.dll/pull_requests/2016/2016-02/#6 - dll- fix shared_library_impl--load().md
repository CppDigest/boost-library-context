# #6 dll: fix shared_library_impl::load() [Closed]

> Username: timblechmann  
> Created at: 2016-02-12 12:21:16 UTC  
> Updated at: 2016-02-13 15:52:14 UTC  
> Closed at: 2016-02-13 15:52:14 UTC  
> Url: https://github.com/boostorg/dll/pull/6  

the apple-codepath calls fs::path::swap, so it cannot be const

---

## Comment 1

> Username: apolukhin  
> Created_at: 2016-02-13 15:52:14 UTC  
> Url: https://github.com/boostorg/dll/pull/6#issuecomment-183693316  

Fixed the issue before noticed this pull request.  
  
Thank you, anyway!

---
