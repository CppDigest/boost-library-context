# #109 Fixed to check readdir_r errno [Closed]

> Username: semun-lee  
> Created at: 2019-04-16 00:31:26 UTC  
> Updated at: 2019-04-16 08:42:17 UTC  
> Closed at: 2019-04-16 08:42:17 UTC  
> Url: https://github.com/boostorg/filesystem/pull/109  

readdir_r() returns errno when it fails.  
errno should be set properly.  
  
Change-Id: Ia79c74bf73ebd53174ba37cb8afd2abda49c47e5  
Signed-off-by: Semun Lee <semun.lee@samsung.com>

---

## Comment 1

> Username: Lastique  
> Created_at: 2019-04-16 08:42:17 UTC  
> Url: https://github.com/boostorg/filesystem/pull/109#issuecomment-483568036  

Should be fixed in 28ccca4.

---
