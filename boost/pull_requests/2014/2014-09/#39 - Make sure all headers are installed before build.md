# #39 Make sure all headers are installed before build [Closed]

> Username: Lastique  
> Created at: 2014-09-15 16:37:12 UTC  
> Updated at: 2018-08-11 20:05:32 UTC  
> Closed at: 2018-08-11 20:05:32 UTC  
> Url: https://github.com/boostorg/boost/pull/39  

Add /boost//headers as a common dependency for all testing tools. The implicit dependency that was used before didn't work:  
  
http://boost.2283326.n4.nabble.com/testing-mpl-core-tests-not-visible-tt4667495.html#a4667510

---

## Comment 1

> Username: Lastique  
> Created_at: 2014-09-23 06:52:21 UTC  
> Url: https://github.com/boostorg/boost/pull/39#issuecomment-56482098  

Now that MPL in develop was reverted, in order to reproduce the problem you will need to checkout modularized MPL from my branch:  
  
https://github.com/lastique/mpl/tree/modularization

---

## Comment 2

> Username: Lastique  
> Created_at: 2014-10-01 08:38:58 UTC  
> Url: https://github.com/boostorg/boost/pull/39#issuecomment-57434569  

Any progress so far? Is there something I can do to help?

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-10-26 13:39:50 UTC  
> Url: https://github.com/boostorg/boost/pull/39#issuecomment-339669073  

Is this PR still valid, need to move forward, etc.?

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-05-01 01:56:46 UTC  
> Url: https://github.com/boostorg/boost/pull/39#issuecomment-385580406  

Is this PR still valid, need to move forward, etc.?  Needs to be rebased at least.  Who is responsible for merging PRs into boostorg/boost?

---

## Comment 5

> Username: swatanabe  
> Created_at: 2018-05-01 02:14:31 UTC  
> Url: https://github.com/boostorg/boost/pull/39#issuecomment-385582573  

AMDG  
  
On 04/30/2018 07:56 PM, James E. King III wrote:  
> Is this PR still valid, need to move forward, etc.?  Needs to be rebased at least.  Who is responsible for merging PRs into boostorg/boost?  
>   
  
The relevant code has been moved to boostorg/regression.  
  
In Christ,  
Steven Watanabe

---

## Comment 6

> Username: jeking3  
> Created_at: 2018-08-01 16:40:40 UTC  
> Url: https://github.com/boostorg/boost/pull/39#issuecomment-409639927  

Is this PR still valid?

---

## Comment 7

> Username: grafikrobot  
> Created_at: 2018-08-11 01:05:09 UTC  
> Url: https://github.com/boostorg/boost/pull/39#issuecomment-412240124  

This PR is no longer valid.

---

## Comment 8

> Username: mloskot  
> Created_at: 2018-08-11 08:50:44 UTC  
> Url: https://github.com/boostorg/boost/pull/39#issuecomment-412261544  

So, why not close it?

---

## Comment 9

> Username: grafikrobot  
> Created_at: 2018-08-11 13:30:34 UTC  
> Url: https://github.com/boostorg/boost/pull/39#issuecomment-412275210  

Yes, @mloskot someone with the appropriate permissions should close it.

---

## Comment 10

> Username: mjcaisse  
> Created_at: 2018-08-11 20:05:32 UTC  
> Url: https://github.com/boostorg/boost/pull/39#issuecomment-412298547  

Closed per conversation above.

---
