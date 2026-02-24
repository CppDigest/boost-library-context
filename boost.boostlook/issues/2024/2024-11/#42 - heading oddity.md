# #42 - heading oddity [Closed]

> Username: rbbeeston  
> Created at: 2024-11-15 04:22:31 UTC  
> Updated at: 2024-12-06 22:50:48 UTC  
> Closed at: 2024-12-06 22:50:48 UTC  
> Url: https://github.com/boostorg/boostlook/issues/42  

Noticed this in the "Any" library h1 header on stage. Seems like` #LibraryReadMe h1 a` was throwing it off with the `float:right`, can you take a look?  
It should be at the top and left justified.  
![Image](https://github.com/user-attachments/assets/2bc9cadc-ad53-46f6-a5de-e6c5f792fe43)

---

## Comment 1

> Username: rbbeeston  
> Created at: 2024-11-15 04:24:54 UTC  
> Url: https://github.com/boostorg/boostlook/issues/42#issuecomment-2477931648  

@julioest @cdw9 we may need to figure out with boostlook what we need to keep with the #LibraryReadMe styles, and what we need to remove or move into boostlook.

---

## Comment 2

> Username: julioest  
> Created at: 2024-11-15 16:15:12 UTC  
> Url: https://github.com/boostorg/boostlook/issues/42#issuecomment-2479334556  

@rbbeeston Yes, we need to eliminate (or move) styles from **website-v2** `frontend/*.css` that conflict with boostlook. Would you like @cdw9 or myself to take this on?  
  
For this specific use case, here are the lines we're using to target the library readmes. Thankfully there's not a lot, so this would be a great place to start: https://github.com/boostorg/website-v2/blob/develop/frontend/styles.css#L99-L156

---

## Comment 3

> Username: rbbeeston  
> Created at: 2024-11-15 16:35:48 UTC  
> Url: https://github.com/boostorg/boostlook/issues/42#issuecomment-2479389885  

Either one of you is fine, it's a small item, however we may want to try to either merge and/or remove that CSS completely from any boostlook styling, as it's only going to conflict. I don't want that to slow down Chrissy from what she's doing, as this is more of a maintenance task than a styling one.  So if you have the bandwidth to look at it, then go ahead, otherwise it can go to her.

---

## Comment 4

> Username: julioest  
> Created at: 2024-11-15 16:58:12 UTC  
> Url: https://github.com/boostorg/boostlook/issues/42#issuecomment-2479434361  

Roger that, I'm on it!

---

## Comment 5

> Username: julioest  
> Created at: 2024-12-06 22:50:48 UTC  
> Url: https://github.com/boostorg/boostlook/issues/42#issuecomment-2524526585  

The issue has been resolved. The heading is now properly aligned to the top and left justified as expected. Closing this issue.
