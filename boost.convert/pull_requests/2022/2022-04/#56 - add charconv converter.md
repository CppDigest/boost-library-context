# #56 add charconv converter [Closed]

> Username: dvirtz  
> Created at: 2022-04-23 21:45:56 UTC  
> Updated at: 2022-04-24 08:07:09 UTC  
> Closed at: 2022-04-24 03:47:16 UTC  
> Url: https://github.com/boostorg/convert/pull/56  

This converter is a wrapper around `std::to_chars` and `std::from_chars` and offers great performance with some formatting support.  
  
I didn't check in the html files because, for some reason, they contain many changes not related to this PR.

---

## Comment 1

> Username: yet-another-user  
> Created_at: 2022-04-24 03:46:59 UTC  
> Url: https://github.com/boostorg/convert/pull/56#issuecomment-1107701978  

Merged. Tnx. Did not do the straight merge. Merged into my own https://github.com/yet-another-user/convert fork first to compile, test, adjust, etc. It did not compile on my gcc-9 as it does not have the full charconv support. Then I merged it all into the boostorg/convert develop branch. Tnx again.

---

## Comment 2

> Username: dvirtz  
> Created_at: 2022-04-24 06:50:09 UTC  
> Url: https://github.com/boostorg/convert/pull/56#issuecomment-1107765739  

Thanks for the quick merge. Do you expect it to be released on `1.80.0`?

---

## Comment 3

> Username: yet-another-user  
> Created_at: 2022-04-24 08:07:09 UTC  
> Url: https://github.com/boostorg/convert/pull/56#issuecomment-1107786818  

Just pushed to master. Should be in the next release.

---
