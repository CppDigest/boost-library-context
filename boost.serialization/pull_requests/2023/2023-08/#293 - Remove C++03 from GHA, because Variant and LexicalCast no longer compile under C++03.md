# #293 Remove C++03 from GHA, because Variant and LexicalCast no longer compile under C++03 [Merged]

> Username: pdimov  
> Created at: 2023-08-25 21:00:22 UTC  
> Updated at: 2023-08-27 15:55:41 UTC  
> Merged at: 2023-08-27 15:55:41 UTC  
> Closed at: 2023-08-27 15:55:41 UTC  
> Url: https://github.com/boostorg/serialization/pull/293  

This pull request removes the C++03 jobs from the Github Actions .yml files, because Boost.Variant and Boost.LexicalCast no longer compile under C++03, which makes all jobs fail.

---
