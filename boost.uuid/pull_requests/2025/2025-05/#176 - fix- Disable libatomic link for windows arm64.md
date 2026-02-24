# #176 fix: Disable libatomic link for windows arm64 [Merged]

> Username: rbqvq  
> Created at: 2025-05-25 06:08:56 UTC  
> Updated at: 2025-08-03 10:33:09 UTC  
> Merged at: 2025-05-25 16:36:54 UTC  
> Closed at: 2025-05-25 16:36:54 UTC  
> Url: https://github.com/boostorg/uuid/pull/176  

CLANGARM64 does not have libatomic, disable it

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-05-25 15:21:39 UTC  
> Url: https://github.com/boostorg/uuid/pull/176#issuecomment-2907889668  

Your current `if` only checks for `MINGW`, which covers both GCC and Clang, whereas the comment refers to Clang. Is `libatomic` needed for GCC MinGW/ARM64?

---

## Comment 2

> Username: rbqvq  
> Created_at: 2025-05-25 15:24:58 UTC  
> Updated_at: 2025-05-25 15:25:14 UTC  
> Url: https://github.com/boostorg/uuid/pull/176#issuecomment-2907891290  

> Your current `if` only checks for `MINGW`, which covers both GCC and Clang, whereas the comment refers to Clang. Is `libatomic` needed for GCC MinGW/ARM64?  
  
So has any MinGW environment with arm64 exists?  
  
I heard it from phoronix in May 7.  
But MSYS2 not support it, So i can not test it.

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-05-25 15:28:19 UTC  
> Url: https://github.com/boostorg/uuid/pull/176#issuecomment-2907892819  

OK, we'll keep it this way until a working GCC appears.

---

## Comment 4

> Username: rbqvq  
> Created_at: 2025-05-25 15:39:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/176#issuecomment-2907901495  

> OK, we'll keep it this way until a working GCC appears.  
  
How long will it take? Will the build fail if libatomic is not linked?

---

## Comment 5

> Username: pdimov  
> Created_at: 2025-05-25 15:43:03 UTC  
> Url: https://github.com/boostorg/uuid/pull/176#issuecomment-2907903019  

I meant that I'll merge your PR as-is for now, and we can tweak the condition at a later date when we can test on GCC.

---

## Comment 6

> Username: rbqvq  
> Created_at: 2025-05-25 15:44:12 UTC  
> Url: https://github.com/boostorg/uuid/pull/176#issuecomment-2907903470  

Oh, Thank you!

---

## Comment 7

> Username: pdimov  
> Created_at: 2025-07-22 18:26:08 UTC  
> Url: https://github.com/boostorg/uuid/pull/176#issuecomment-3104252633  

I suspect that we no longer need this after https://github.com/boostorg/uuid/commit/434329f8dc7fcfb69cd7565ab0318d86772cea39. Can you please check?

---

## Comment 8

> Username: rbqvq  
> Created_at: 2025-07-23 04:23:48 UTC  
> Url: https://github.com/boostorg/uuid/pull/176#issuecomment-3105633377  

I think it should be all right.  
But the ci build failed due to other problems, I'll give you the answer when the fix is done. :(

---

## Comment 9

> Username: rbqvq  
> Created_at: 2025-08-03 10:33:08 UTC  
> Url: https://github.com/boostorg/uuid/pull/176#issuecomment-3148327753  

You can safety revert. It works.

---
