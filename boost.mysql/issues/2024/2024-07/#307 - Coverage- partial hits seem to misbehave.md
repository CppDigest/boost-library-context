# #307 - Coverage: partial hits seem to misbehave [Closed]

> Username: anarthal  
> Created at: 2024-07-08 15:14:44 UTC  
> Updated at: 2024-07-24 10:16:47 UTC  
> Closed at: 2024-07-24 10:16:46 UTC  
> Url: https://github.com/boostorg/mysql/issues/307  

It looks like codecov is just ignoring lines with partial coverage, which is inconsistent with that their docs say. Additionally, some lines in nested function calls are reported to be uncovered, being covered. We may need to introduce some external tool to handle coverage.
