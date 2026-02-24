# #31 Add explicit tests around use of `scoped_allocator_adaptor` to emulat… [Merged]

> Username: cmazakas  
> Created at: 2021-09-21 03:09:58 UTC  
> Updated at: 2021-12-28 16:18:16 UTC  
> Merged at: 2021-11-15 19:22:40 UTC  
> Closed at: 2021-11-15 19:22:40 UTC  
> Url: https://github.com/boostorg/unordered/pull/31  

…e issue https://github.com/boostorg/unordered/issues/22

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2021-10-02 17:09:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/31#issuecomment-932785755  

What's going on with the build failures?

---

## Comment 2

> Username: cmazakas  
> Created_at: 2021-10-04 22:20:18 UTC  
> Url: https://github.com/boostorg/unordered/pull/31#issuecomment-933900063  

> What's going on with the build failures?  
  
Good news, I'm able to replicate the test failure locally. It only manifested in msvc 16.11.3.  
  
I was using 16.11.1 and for some reason couldn't get it to fail so I updated my Visual Studio install and lo and behold, I have a local failure.

---

## Comment 3

> Username: cmazakas  
> Created_at: 2021-10-10 15:07:52 UTC  
> Url: https://github.com/boostorg/unordered/pull/31#issuecomment-939498686  

The remaining test failure on AppVeyor seems to be a known issue. I'll leave the PR as-is until it gets fixed upstream and then we can merge in.

---

## Comment 4

> Username: pdimov  
> Created_at: 2021-10-18 23:31:35 UTC  
> Updated_at: 2021-10-18 23:33:23 UTC  
> Url: https://github.com/boostorg/unordered/pull/31#issuecomment-946246862  

In cases like these, when just one test needs `/boost//filesystem`, it's better to put the library requirement on the test itself, rather than in the project requirements. (Project requirements apply to every target in the project.)  
  
The easiest way to do this is `run unordered/scoped_allocator.cpp /boost//filesystem`, but you can use a `<library>` requirement as well, if you prefer.  
  
(As an example of the difference, if building Filesystem fails, putting it in the project requirements will cause all the tests to fail, whereas putting it on one test will only cause that one test to fail in this case.)

---
