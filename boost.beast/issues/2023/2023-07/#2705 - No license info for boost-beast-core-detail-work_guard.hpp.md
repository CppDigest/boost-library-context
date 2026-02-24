# #2705 - No license info for boost/beast/core/detail/work_guard.hpp [Closed]

> Username: jwakely  
> Created at: 2023-07-13 12:57:31 UTC  
> Updated at: 2023-08-18 10:46:30 UTC  
> Closed at: 2023-08-18 10:46:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2705  

https://github.com/boostorg/beast/blob/develop/include/boost/beast/core/detail/work_guard.hpp has no copyright or license info. I assume it's covered by the usual Boost license, but it would be better to say so in the file.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-07-13 13:49:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2705#issuecomment-1634281917  

Legally speaking, of course it is based on the license file in the project root. I'll add the license after the 1.83. release is done.

---

## Comment 2

> Username: jwakely  
> Created at: 2023-07-13 13:51:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2705#issuecomment-1634285785  

The presence of a license file in the project doesn't imply that all files are covered by that license (e.g. some files in the project are also covered by the Zlib license). And the license is separate from the copyright info, which is also missing.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-07-13 13:54:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2705#issuecomment-1634291145  

We can add the license now since it is documentation/comments, and those merges require no permission.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2023-07-13 14:33:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2705#issuecomment-1634355720  

I'd prefer not to, just to keep the release history completely clean.
