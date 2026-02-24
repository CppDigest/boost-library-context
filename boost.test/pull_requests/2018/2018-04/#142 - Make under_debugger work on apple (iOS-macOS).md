# #142 Make under_debugger work on apple (iOS/macOS) [Closed]

> Username: thughes  
> Created at: 2018-04-26 17:17:45 UTC  
> Updated at: 2022-02-27 19:46:34 UTC  
> Closed at: 2020-05-17 10:05:41 UTC  
> Url: https://github.com/boostorg/test/pull/142  

Simply uses the documented way from Apple to detect the debugger: https://developer.apple.com/library/content/qa/qa1361/_index.html

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2018-06-26 20:25:17 UTC  
> Url: https://github.com/boostorg/test/pull/142#issuecomment-400449773  

Compilation and tests are passing, but how do I test for this?

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2018-06-26 20:32:38 UTC  
> Url: https://github.com/boostorg/test/pull/142#issuecomment-400452158  

There is also this: https://zgcoder.net/ramblings/osx-debugger-detection.html and the approach in the PR is said to be for debug builds only.

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2020-05-17 10:05:41 UTC  
> Url: https://github.com/boostorg/test/pull/142#issuecomment-629773022  

In master, thanks!

---
