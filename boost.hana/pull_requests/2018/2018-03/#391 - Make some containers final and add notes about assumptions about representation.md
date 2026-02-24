# #391 Make some containers final and add notes about assumptions about representation [Merged]

> Username: ldionne  
> Created at: 2018-03-05 21:38:45 UTC  
> Updated at: 2018-05-06 16:09:29 UTC  
> Merged at: 2018-05-06 16:08:50 UTC  
> Closed at: 2018-05-06 16:08:50 UTC  
> Url: https://github.com/boostorg/hana/pull/391  

Also, add tests to make sure that an empty pair can be EBO'd. This one is very important because a typical use case is to create a tuple of pairs of empty types (e.g. in `hana::map`), and we expect this to be empty.  
  
Fixes #388

---

## Comment 1

> Username: ricejasonf  
> Created_at: 2018-03-05 22:22:16 UTC  
> Url: https://github.com/boostorg/hana/pull/391#issuecomment-370588545  

Perhaps `hana::map<???>` and others should be in the `detail` namespace.

---

## Comment 2

> Username: ldionne  
> Created_at: 2018-05-06 16:09:29 UTC  
> Url: https://github.com/boostorg/hana/pull/391#issuecomment-386890348  

> Perhaps hana::map<???> and others should be in the detail namespace.  
  
No, because `hana::map<...>` can be used to specify the type of a map. The type exists, it's just that what the type is is unspecified.

---
