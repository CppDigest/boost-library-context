# #264 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:31:19 UTC  
> Updated at: 2025-01-07 17:12:35 UTC  
> Merged at: 2024-08-28 10:36:48 UTC  
> Closed at: 2024-08-28 10:36:48 UTC  
> Url: https://github.com/boostorg/context/pull/264  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 16:02:31 UTC  
> Url: https://github.com/boostorg/context/pull/264#issuecomment-2295310928  

Please review and merge this PR at your earliest convenience.

---

## Comment 2

> Username: olk  
> Created_at: 2024-08-18 18:43:01 UTC  
> Url: https://github.com/boostorg/context/pull/264#issuecomment-2295353585  

> Please review and merge this PR at your earliest convenience.  
  
Sorry - some checks are faling (11 failing and 12 successful checks)

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2024-08-18 19:08:02 UTC  
> Url: https://github.com/boostorg/context/pull/264#issuecomment-2295359531  

The failures look unrelated to my changes. Especially since the checkout itself is failing.

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2024-08-24 02:31:37 UTC  
> Url: https://github.com/boostorg/context/pull/264#issuecomment-2308001863  

@olk can you merge this https://github.com/boostorg/context/pull/257 that fixes the CI setup please.

---

## Comment 5

> Username: olk  
> Created_at: 2024-10-08 09:25:43 UTC  
> Updated_at: 2024-10-08 09:25:58 UTC  
> Url: https://github.com/boostorg/context/pull/264#issuecomment-2399330433  

Since https://github.com/boostorg/context/commit/463cffeac5fdaa712be316654a9e61b77cc358ec CI builds are broken on some platforms!

---

## Comment 6

> Username: pdimov  
> Created_at: 2025-01-07 15:07:06 UTC  
> Url: https://github.com/boostorg/context/pull/264#issuecomment-2575530545  

The use of `[ predef.architecture ]` introduced by this PR [breaks debian/ppc64le](https://github.com/boostorg/context/actions/runs/12653459340/job/35258857413) because Predef [incorrectly returns `ppc` as the architecture](https://github.com/boostorg/predef/blob/9aca7f5b609a731106a6d70e8dca9a4196dca968/tools/check/predef.jam#L88), which isn't a valid value for the feature. The correct one is `power`, which `architecture.jam` correctly returns:  
  
https://github.com/boostorg/context/blob/d11cbccc87da5d6d41c04f3949e18d49c43e62fc/build/architecture.jam#L68  
  
Predef probably needs to be fixed.  
  
There are three additional failures in multi.yml, but I don't know what they are caused by. Maybe @Kojoley, who wrote multi.yml, would be able to say.

---

## Comment 7

> Username: pdimov  
> Created_at: 2025-01-07 15:11:33 UTC  
> Url: https://github.com/boostorg/context/pull/264#issuecomment-2575540425  

https://github.com/boostorg/predef/issues/137

---

## Comment 8

> Username: Kojoley  
> Created_at: 2025-01-07 15:35:49 UTC  
> Url: https://github.com/boostorg/context/pull/264#issuecomment-2575597007  

> Maybe @Kojoley, who wrote multi.yml, would be able to say.  
  
I'm sorry, I have no idea. Predef could be absolutely in the wrongs, like in this case https://github.com/boostorg/predef/issues/127. But the fundamentals of this project build script is a primary issue and I had too many attempts at improving the situation.

---

## Comment 9

> Username: pdimov  
> Created_at: 2025-01-07 17:04:05 UTC  
> Url: https://github.com/boostorg/context/pull/264#issuecomment-2575809492  

Did those CI jobs work when you originally wrote multi.yml? We need to figure out what broke them.

---

## Comment 10

> Username: pdimov  
> Created_at: 2025-01-07 17:04:46 UTC  
> Url: https://github.com/boostorg/context/pull/264#issuecomment-2575810823  

https://github.com/boostorg/predef/issues/135 seems like a likely cause, too.

---

## Comment 11

> Username: pdimov  
> Created_at: 2025-01-07 17:12:34 UTC  
> Url: https://github.com/boostorg/context/pull/264#issuecomment-2575826701  

OK, the answer is yes, they did work before this PR was merged: https://github.com/boostorg/context/actions/runs/10594678160

---
