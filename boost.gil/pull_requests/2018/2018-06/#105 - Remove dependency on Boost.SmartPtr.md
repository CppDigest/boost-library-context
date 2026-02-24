# #105 Remove dependency on Boost.SmartPtr [Merged]

> Username: mloskot  
> Created at: 2018-06-24 19:56:22 UTC  
> Updated at: 2018-06-25 07:44:43 UTC  
> Merged at: 2018-06-25 07:44:38 UTC  
> Closed at: 2018-06-25 07:44:39 UTC  
> Url: https://github.com/boostorg/gil/pull/105  

### Description: what does this pull request do?  
  
Replace boost::shared_ptr with C++11 std::shared_ptr.  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed (except `ubsan_integer` still expected to fail)

---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2018-06-24 21:48:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/105#pullrequestreview-131437628  

📁 .travis.yml

```diff
 195 |   - git submodule --quiet update --init libs/rational
 196 |   - git submodule --quiet update --init libs/regex
 197 |+   - git submodule --quiet update --init libs/smart_ptr
```

> Username: stefanseefeld  
> Created_at: 2018-06-24 21:48:39 UTC  
> Updated_at: 2018-06-24 22:13:41 UTC  
> Url: https://github.com/boostorg/gil/pull/105#discussion_r197653975  

why did you add it here ? I thought we no longer needed it ?

> Username: mloskot  
> Created_at: 2018-06-24 21:56:08 UTC  
> Updated_at: 2018-06-24 22:13:41 UTC  
> Url: https://github.com/boostorg/gil/pull/105#discussion_r197654149  

It moved from group of direct deps to transitive deps. - it still is required by some other Boost libs.

> Username: stefanseefeld  
> Created_at: 2018-06-24 22:31:10 UTC  
> Updated_at: 2018-06-24 22:31:29 UTC  
> Url: https://github.com/boostorg/gil/pull/105#discussion_r197654979  

I see. (Then please leave a comment to make that clear.)

> Username: mloskot  
> Created_at: 2018-06-24 22:44:48 UTC  
> Updated_at: 2018-06-24 22:45:23 UTC  
> Url: https://github.com/boostorg/gil/pull/105#discussion_r197655323  

Comment where? commit log, .travis.yml  
  
The .travis.yml already has comments indicating if a library belongs to direct or transitive dependencies:  
  
https://github.com/mloskot/gil/blob/0eae37af54b6de79dc4dc0d0255b76154daef66f/.travis.yml#L155-L157  
  
https://github.com/mloskot/gil/blob/0eae37af54b6de79dc4dc0d0255b76154daef66f/.travis.yml#L173-L175

> Username: stefanseefeld  
> Created_at: 2018-06-25 00:27:54 UTC  
> Url: https://github.com/boostorg/gil/pull/105#discussion_r197658780  

OK, fair enough.


---
