# #38 - When boostlook is moved to stage, use that to trigger moving boostlook to website-v2 develop [Closed]

> Username: rbbeeston  
> Created at: 2024-11-13 20:54:02 UTC  
> Updated at: 2024-11-15 16:18:59 UTC  
> Closed at: 2024-11-15 16:17:46 UTC  
> Url: https://github.com/boostorg/boostlook/issues/38  

To avoid the issues we have had with updating boostlook. instead of the trigger to move from master to master, we would move from develop to develop, and then the current boostlook would go to website-v2 master during the weekly merge, avoiding any other issues.  
  
@sdarwin this is basically what we discussed, let me know if you have additional thoughts or concerns.

---

## Comment 1

> Username: sdarwin  
> Created at: 2024-11-13 21:21:30 UTC  
> Url: https://github.com/boostorg/boostlook/issues/38#issuecomment-2474809676  

if you would like to try that.  
  
> instead of the trigger to move from master to master  
  
( before it was triggering from master (boostlook) to develop (website-v2) and then merged weekly to master (website-v2).  )

---

## Comment 2

> Username: rbbeeston  
> Created at: 2024-11-14 06:56:38 UTC  
> Url: https://github.com/boostorg/boostlook/issues/38#issuecomment-2475556527  

@sdarwin, how it was set up before would be fine, except for having to wait for a month for boostlook master to be updated during the code freeze before the release. This solution will allow continued development and merging without affecting the code freeze, and can be synced up afterwards.

---

## Comment 3

> Username: julioest  
> Created at: 2024-11-15 16:17:46 UTC  
> Updated at: 2024-11-15 16:18:59 UTC  
> Url: https://github.com/boostorg/boostlook/issues/38#issuecomment-2479339156  

This has been addressed in https://github.com/boostorg/boostlook/pull/41 – closing issue.
