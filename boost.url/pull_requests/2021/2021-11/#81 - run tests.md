# #81 run tests [Closed]

> Username: sdarwin  
> Created at: 2021-11-22 14:48:02 UTC  
> Updated at: 2021-12-17 17:34:18 UTC  
> Closed at: 2021-12-17 17:34:17 UTC  
> Url: https://github.com/boostorg/url/pull/81  

Don't merge.  Trigger existing ci tests, without other changes, checking the build status.

---

## Comment 1

> Username: sdarwin  
> Created_at: 2021-11-22 15:58:19 UTC  
> Url: https://github.com/boostorg/url/pull/81#issuecomment-975670528  

The test demonstrates that .azure-pipelines.yml will probably need to be updated.  ok.   
  
Also another interesting result, one target on msvc-14.2 has been consistently failing. This isn't "random", same result last week.     
  
@vinniefalco any idea?  
  
https://github.com/CPPAlliance/url/runs/4287984429?check_suite_focus=true  
  
```  
compile-c-c++ bin.v2\libs\url\test\unit\segments_encoded_view.test\msvc-14.2\debug\address-model-32\cxxstd-20-iso\threading-multi\segments_encoded_view.obj  
segments_encoded_view.cpp  
msvc.link bin.v2\libs\url\test\unit\segments_encoded_view.test\msvc-14.2\debug\address-model-32\cxxstd-20-iso\threading-multi\segments_encoded_view.exe  
testing.capture-output bin.v2\libs\url\test\unit\segments_encoded_view.test\msvc-14.2\debug\address-model-32\cxxstd-20-iso\threading-multi\segments_encoded_view.run  
====== BEGIN OUTPUT ======  
boost.url.segments_encoded_view  
#51 segments_encoded_view.cpp(86) failed: v0 == v1  
```

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2021-11-23 02:15:43 UTC  
> Url: https://github.com/boostorg/url/pull/81#issuecomment-976104263  

No idea about that failing test.

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2021-11-23 02:16:00 UTC  
> Url: https://github.com/boostorg/url/pull/81#issuecomment-976104364  

@alandefreitas want to look into the msvc test failure?

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2021-11-23 03:51:00 UTC  
> Url: https://github.com/boostorg/url/pull/81#issuecomment-976148244  

> @alandefreitas want to look into the msvc test failure?  
  
Sure 👍

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2021-12-17 17:01:33 UTC  
> Url: https://github.com/boostorg/url/pull/81#issuecomment-996876919  

So...what is the status of this?

---

## Comment 6

> Username: alandefreitas  
> Created_at: 2021-12-17 17:28:44 UTC  
> Updated_at: 2021-12-17 17:29:52 UTC  
> Url: https://github.com/boostorg/url/pull/81#issuecomment-996895429  

> So...what is the status of this?  
  
We identified and fixed the problem a couple of weeks ago: `segments_encoded::iterator::operator*` should have been returning a `reference`, which is a `string_view`, because of how C++20 defines `iterator_traits::reference`.  
  
For instance, [these tests](https://github.com/CPPAlliance/url/pull/87), which are more recent, didn't fail for "CI / windows (msvc-14.2, 14,17,20, 32,64, windows-2019) (pull_request)". I think the results in this PR are just old. It failed 25 days ago.  
  
<img width="448" alt="image" src="https://user-images.githubusercontent.com/5369819/146584390-82a9a17d-87f7-4d90-b2bd-007f595cffd5.png">  
  
Anyway, the problem was difficult to identify but quite easy to fix. Less than 10 `char`s. 🙂 If I'm wrong about this and this is not fixed yet, I can open a PR.

---
