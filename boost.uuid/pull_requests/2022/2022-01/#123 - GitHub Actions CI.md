# #123 GitHub Actions CI [Merged]

> Username: jeking3  
> Created at: 2022-01-25 21:46:27 UTC  
> Updated at: 2022-01-25 22:25:21 UTC  
> Merged at: 2022-01-25 21:51:29 UTC  
> Closed at: 2022-01-25 21:51:30 UTC  
> Url: https://github.com/boostorg/uuid/pull/123  

- replaces travis  
- replaces appveyor  
- does not test cmake (was failing)  
- does not test big-endian at this time  
- will open issues for cmake, cygwin, big-endian testing  
- is better than no CI at all!  
  
Took the template from [boostorg/boost-ci](https://github.com/boostorg/boost-ci/blob/master/.github/workflows/ci.yml).  
  
This closes #119   
This closes #118

---

## Comment 1

> Username: jeking3  
> Created_at: 2022-01-25 21:51:25 UTC  
> Url: https://github.com/boostorg/uuid/pull/123#issuecomment-1021643086  

I tested this in my fork of the repo, merging and will fix up if anything comes of it.

---
