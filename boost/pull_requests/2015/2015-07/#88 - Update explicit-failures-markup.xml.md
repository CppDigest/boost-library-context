# #88 Update explicit-failures-markup.xml [Merged]

> Username: apolukhin  
> Created at: 2015-07-01 20:32:53 UTC  
> Updated at: 2015-07-04 12:18:22 UTC  
> Merged at: 2015-07-02 18:29:32 UTC  
> Closed at: 2015-07-02 18:29:32 UTC  
> Url: https://github.com/boostorg/boost/pull/88  

Mark some failures for the type_index library as expected.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-07-04 11:16:26 UTC  
> Url: https://github.com/boostorg/boost/pull/88#issuecomment-118501031  

There is a problem with failures markup. In the regression matrix the test with intel compiler is still expected to pass and with gcc-4.5 to fail. This is true for both master and develop branches of TypeIndex. I think the reason is that Regression script uses the version of the markup file always from Boost master branch. See the file: https://github.com/boostorg/regression/blob/develop/reports/src/build_results_all.sh lines 20 and then 62.  
I wanted to create a PR for Regression but I don't know what should be the intended behavior. Should the markup file from master branch be used now? This is why I prepared a PR updating the master branch https://github.com/boostorg/boost/pull/82 but since it wasn't merged I guess it isn't prefered, or is it? Maybe then the markup file from develop branch be always used? Or should the markup file from the corresponding branch be used (develop for develop and master for master)?

---

## Comment 2

> Username: apolukhin  
> Created_at: 2015-07-04 12:18:22 UTC  
> Url: https://github.com/boostorg/boost/pull/88#issuecomment-118507824  

The answer to this question is totally out of core boost project scope.  
  
Right solution would be to break down the `explicit-failures-markup.xml` into small pieces and put each piece into the `meta/` folder in each library. In that case there'll be no wired pull requests to boost core project and each library maintainer could easily modify it.  
  
This is a big job that must be done some day by a brave and enthusiastic man. I'm not such a guy :(  
  
Until then, a quick workaround could be to automatically/by-hand sync the `explicit-failures-markup.xml` in master and develop branches.  
  
P.S.: infinite thanks for patching the regression summary reports. It's now a high quality tool that is great to have!

---
