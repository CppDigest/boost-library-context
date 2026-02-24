# #265 fix(engine): add quote around $(CC) in src/engine/build.jam [Merged]

> Username: ledocc  
> Created at: 2017-11-23 05:43:43 UTC  
> Updated at: 2021-10-02 21:19:08 UTC  
> Merged at: 2017-11-23 14:09:26 UTC  
> Closed at: 2017-11-23 14:09:26 UTC  
> Url: https://github.com/boostorg/build/pull/265  

- when CC environment variable is defined with a path that contain space,  
the build fail. just add quote around usage of CC variable did the trick  
  
fixes #239

---

## Comment 1

> Username: swatanabe  
> Created_at: 2018-01-24 17:13:28 UTC  
> Url: https://github.com/boostorg/build/pull/265#issuecomment-360205973  

This patch is wrong.  The quotes don't do anything.  It was also applied to master without being applied to develop first.  I'm reverting it.

---

## Comment 2

> Username: ledocc  
> Created_at: 2018-01-24 18:03:57 UTC  
> Url: https://github.com/boostorg/build/pull/265#issuecomment-360221511  

On windows, with CC=c:\Path to my compiler\cl.exe, the build fails due to space in path.

---

## Comment 3

> Username: swatanabe  
> Created_at: 2018-01-24 18:14:14 UTC  
> Url: https://github.com/boostorg/build/pull/265#issuecomment-360224463  

AMDG  
  
On 01/24/2018 11:04 AM, David Callu wrote:  
> On windows, with CC=c:\Path to my compiler\cl.exe, the build fails due to space in path.  
>   
  
True enough, but the patch doesn't fix that.  It still fails.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: pdimov  
> Created_at: 2019-03-24 16:41:09 UTC  
> Url: https://github.com/boostorg/build/pull/265#issuecomment-475976034  

True enough. Why does it fail? I would have expected `"$(CC)"` to work, but it doesn't.

---

## Comment 5

> Username: pdimov  
> Created_at: 2019-03-24 16:44:20 UTC  
> Url: https://github.com/boostorg/build/pull/265#issuecomment-475976387  

Ah it needs to be `\"$(CC:J= )\"`.

---

## Comment 6

> Username: pdimov  
> Created_at: 2019-03-24 16:49:57 UTC  
> Url: https://github.com/boostorg/build/pull/265#issuecomment-475977041  

Or just `$(CC:J= )` as @grafikrobot points out.

---

## Comment 7

> Username: pdimov  
> Created_at: 2019-03-24 17:21:28 UTC  
> Url: https://github.com/boostorg/build/pull/265#issuecomment-475980165  

See #415.

---
