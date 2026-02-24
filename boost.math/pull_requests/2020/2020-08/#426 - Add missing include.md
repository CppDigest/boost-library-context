# #426 Add missing include [Closed]

> Username: camolezi  
> Created at: 2020-08-27 14:03:09 UTC  
> Updated at: 2020-08-28 12:27:01 UTC  
> Closed at: 2020-08-28 12:24:24 UTC  
> Url: https://github.com/boostorg/math/pull/426  

This patch makes the header able to be built standalone, making possible C++ clang modules builds.

---

## Comment 1

> Username: pulver  
> Created_at: 2020-08-27 17:02:54 UTC  
> Url: https://github.com/boostorg/math/pull/426#issuecomment-682074027  

Hi @camolezi - thank you for the PR.  
  
Is it possible you are/were using an older version of boost? This PR #313 from earlier this year replaced all instances of `decay_t` (with no namespace) with `std::decay`. Or are you still encountering the same issue with current `master`?

---

## Comment 2

> Username: camolezi  
> Created_at: 2020-08-28 12:24:24 UTC  
> Updated_at: 2020-08-28 12:27:01 UTC  
> Url: https://github.com/boostorg/math/pull/426#issuecomment-682496431  

Sorry, we are indeed not using the latest version of boost. Seems like on the current version we don't need this include anymore, thanks for the review.

---
