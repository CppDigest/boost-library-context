# #1437 Move Boost::crc and Boost::program_options dependencies from library to unit and robustness tests [Merged]

> Username: jeremy-murphy  
> Created at: 2025-10-23 02:14:00 UTC  
> Updated at: 2025-10-27 22:48:01 UTC  
> Merged at: 2025-10-26 19:13:48 UTC  
> Closed at: 2025-10-26 19:13:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1437  

Not an actual dependency of the library. Discovered when I tried to make Boost.Geometry a dependency of Boost.Graph, the CMake breaks.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2025-10-23 02:16:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1437#issuecomment-3434827987  

Sorry, might have opened this without doing proper research.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2025-10-23 02:26:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1437#issuecomment-3434842261  

OK, reopened now that I've put the Boost::crc dependency where it needs to go (on the unit tests).

---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2025-10-23 17:40:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1437#pullrequestreview-3371537820  

📁 test/CMakeLists.txt

```diff
  36 |+     PRIVATE
  37 |+       Boost::crc)
  38 |+ 
```

> Username: barendgehrels  
> Created_at: 2025-10-23 17:40:17 UTC  
> Updated_at: 2025-10-23 17:40:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/1437#discussion_r2456354433  

👍 thanks @jeremy-murphy !


---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2025-10-23 22:19:45 UTC  
> Updated_at: 2025-10-24 02:24:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1437#issuecomment-3439455309  

The merge status is unknown and no amount of rechecking is fixing it, so I seem to be blocked from merging.  
  
PS. Which, btw, is a status and button that only seems to appear on the mobile app; at least, I can't find it on the desktop version.

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2025-10-25 09:39:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1437#issuecomment-3446415415  

@barendgehrels are you able to merge this or does it require more approvals? I'm just getting an error from GitHub.

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2025-10-25 10:50:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/1437#issuecomment-3446552778  

> @barendgehrels are you able to merge this or does it require more approvals? I'm just getting an error from GitHub.  
  
I'll check.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2025-10-25 11:17:53 UTC  
> Updated_at: 2025-10-25 11:18:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1437#issuecomment-3446566680  

> > @barendgehrels are you able to merge this or does it require more approvals? I'm just getting an error from GitHub.  
>   
> I'll check.  
  
@jeremy-murphy it does not build for me, because some of the robustness tests don't go via `boost_geometry_add_unit_test`  
  
It works if it is, instead, added in `boost_geometry_add_robustness_test`  
  
Side note:  
There is one additional optimization possible, because we can do exactly the same for `program_options`  
But that requires two extra changes. I can do that in a follow-up if you want.

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2025-10-25 11:28:52 UTC  
> Updated_at: 2025-10-25 11:29:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/1437#issuecomment-3446573880  

@jeremy-murphy    
> Side note: There is one additional optimization possible, because we can do exactly the same for `program_options` But that requires two extra changes. I can do that in a follow-up if you want.  
  
For convenience my diff (with branch `develop`):  
  
[diff_crc.txt](https://github.com/user-attachments/files/23141532/diff_crc.txt)  
  
But **ignore** the removal of `EXCLUDE_FROM_ALL`

---

## Comment 9

> Username: jeremy-murphy  
> Created_at: 2025-10-25 22:35:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/1437#issuecomment-3447834006  

`program_options` would cause the same problem, so I'll fix it here too.

---

## Review 10 [Commented]

> Username: barendgehrels  
> Created_at: 2025-10-26 06:25:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1437#pullrequestreview-3380440047  

📁 test/robustness/CMakeLists.txt

```diff
  20 |+     PRIVATE
  21 |+       Boost::crc
  22 |+       Boost::program_options)
```

> Username: barendgehrels  
> Created_at: 2025-10-26 06:25:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1437#discussion_r2463216422  

This version works for me, thanks.  
  
I will merge it today.

> Username: jeremy-murphy  
> Created_at: 2025-10-26 09:50:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1437#discussion_r2463584783  

Great, thank you!   
I'm so used to merging things myself, I was a little confused when I couldn't find the merge button. 😂

> Username: barendgehrels  
> Created_at: 2025-10-26 19:15:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1437#discussion_r2464021213  

Merged! Thanks again for your contribution!  
It's the setup of our repo, not everyone can merge.


---
