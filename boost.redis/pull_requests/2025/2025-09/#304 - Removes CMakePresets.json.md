# #304 Removes CMakePresets.json [Merged]

> Username: anarthal  
> Created at: 2025-09-14 12:54:11 UTC  
> Updated at: 2025-09-15 12:19:09 UTC  
> Merged at: 2025-09-15 12:19:04 UTC  
> Closed at: 2025-09-15 12:19:04 UTC  
> Url: https://github.com/boostorg/redis/pull/304  



---

## Comment 1

> Username: anarthal  
> Created_at: 2025-09-14 12:55:41 UTC  
> Url: https://github.com/boostorg/redis/pull/304#issuecomment-3289522852  

The presets file contains config for quite old toolchains, and creates problems with my vscode environment (I prefer using kits and settings.json. It looks like a developer-specific file that should probably be excluded from the source tree.

---

## Comment 2

> Username: mzimbres  
> Created_at: 2025-09-14 16:55:03 UTC  
> Url: https://github.com/boostorg/redis/pull/304#issuecomment-3289685889  

> It looks like a developer-specific file that should probably be excluded from the source tree.  
  
`CMakePresets.json` is not user specific, that's `CMakeUserPresets.json`. I don't see how it can cause problem but I haven't been using this file for some time now so we can drop it.

---

## Comment 3

> Username: anarthal  
> Created_at: 2025-09-15 12:19:00 UTC  
> Url: https://github.com/boostorg/redis/pull/304#issuecomment-3291883143  

Apparently vscode strongly prefers CMakePresets.json to kits when it's present. I didn't know that CMakeUserPresets.json was a thing. Since the presets are out of date, I'll merge this. Thanks.

---
